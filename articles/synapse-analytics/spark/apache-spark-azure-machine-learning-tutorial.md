---
title: Руководство по выполнению экспериментов с помощью автоматизированного машинного обучения Azure
description: Руководство по выполнению экспериментов машинного обучения с помощью Apache Spark и автоматизированного машинного обучения Azure
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: b2fbc74304cdb71d9cb3e1ea476af8c92eb99b7e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458838"
---
# <a name="tutorial-run-experiments-using-azure-automated-ml-and-apache-spark"></a>Руководство по выполнению экспериментов с помощью автоматизированного машинного обучения Azure и Apache Spark

Машинное обучение Azure представляет собой облачную среду, которая позволяет обучать, развертывать, автоматизировать, контролировать и отслеживать модели машинного обучения. 

В этом руководстве показано, как в Машинном обучении Azure с помощью функции [автоматизированного машинного обучения](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) создать модель регрессии для прогнозирования тарифов на такси в Нью-Йорке. В ходе этого процесса добавляются данные для обучения и настраиваются параметры, а также выполняется автоматическая итерация с использованием комбинаций разных методов нормализации или стандартизации признаков, моделей и настроек гиперпараметров для получения оптимальной модели.

В этом руководстве описано, как выполнять такие задачи:
- скачивание данных с помощью Apache Spark и Открытых наборов данных Azure;
- преобразование и очистка данных с помощью кадров данных Apache Spark;
- Обучение автоматизированной регрессионной модели машинного обучения.
- Расчет точности модели.

### <a name="before-you-begin"></a>Подготовка к работе

- Создайте бессерверный пул Apache Spark, следуя инструкциям из [этого краткого руководства](../quickstart-create-apache-spark-pool-studio.md).
- Выполните действия из [руководства по настройке рабочей области машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup), если у вас еще нет рабочей области машинного обучения Azure. 

### <a name="understand-regression-models"></a>Общие сведения о моделях регрессии

*Модели регрессии* прогнозируют числовые выходные значения на основе независимых прогностических факторов. В случае регрессии цель заключается в том, чтобы установить связь между этими независимыми переменными прогнозирования, оценивая, как одна переменная влияет на другие.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>Пример регрессионного анализа данных о такси Нью-Йорка

В этом примере вы с помощью Spark выполните некоторый анализ данных о чаевых таксистам Нью-Йорка. Данные доступны через [открытые наборы данных Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Это подмножество набора данных содержит сведения о поездках на такси, включая сведения о каждой поездке, времени начала и окончания и расположениях, стоимости и других интересных атрибутах.

> [!IMPORTANT]
> 
> За получение этих данных из хранилища может взиматься дополнительная плата. В следующих шагах вам предстоит разработать модель для прогнозирования тарифов на такси в Нью-Йорке. 
> 

##  <a name="download-and-prepare-the-data"></a>Скачивание и подготовка данных

1. Создайте записную книжку, используя ядро PySpark. Инструкции см. в разделе [Создание записной книжки](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.).
   
> [!Note]
> 
> Ядро PySpark позволяет не задавать контексты явным образом. Контекст Spark будет создан автоматически при выполнении первой ячейки кода.
>

2. Поскольку необработанные данные имеют формат Parquet, можно использовать контекст Spark, чтобы извлечь файл в память напрямую в виде таблицы данных. Создайте кадр данных Spark, извлекая данные с помощью API Открытых наборов данных. Здесь мы будем использовать свойства *схемы при чтении* для кадра данных Spark, чтобы определить типы и схему данных. 
   
```python
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "yellow"
blob_sas_token = r""

# Allow Spark to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

# Spark read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)

```

3. В зависимости от размера пула Spark объем необработанных данных может быть слишком большим или их обработка может занимать слишком много времени. Эти данные можно сузить до меньшего объема с помощью фильтров ```start_date``` и ```end_date```. Здесь применяется фильтр, который возвращает данные за один месяц. После создания отфильтрованного кадра данных мы выполним функцию ```describe()``` с новым кадром данных, чтобы просмотреть сводную статистику для каждого поля. 

   В сводной статистике можно заметить, что в данных есть некоторое количество отклонений и выбросов. Например, минимальное расстояние поездки в этой статистике имеет отрицательное значение. Нам придется отфильтровать некорректные точки данных.
   
```python
# Create an ingestion filter
start_date = '2015-01-01 00:00:00'
end_date = '2015-12-31 00:00:00'

filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

filtered_df.describe().show()
```

4. Теперь мы будем создавать признаки по этому набору данных, выбирая набор столбцов и выбирая связанные с временем признаки из поля для выбора дат и времени. Мы также отфильтруем обнаруженные на предыдущем шаге выбросы, а затем удалим несколько последних столбцов, которые не нужны для обучения.
   
```python
from datetime import datetime
from pyspark.sql.functions import *

# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                , column('puMonth').alias('month_num') \
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                & (sampled_taxi_df.rateCodeId <= 5)\
                                & (sampled_taxi_df.paymentType.isin({"1", "2"})))
taxi_df.show(10)
```
   
   Как видите, в результате создается новый кадр данных, где есть дополнительные столбцы для дня месяца, часа посадки в такси, дня недели и общего времени поездки. 


![Изображение кадра данных о такси.](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>Создание наборов данных для тестирования и проверки

Получив итоговый набор данных, нам нужно разделить эти данные на обучающий и проверочный наборы данных с помощью функции Spark ```random_ split ```. Используя предоставленные весовые коэффициенты, эта функция случайным образом разбивает данные на обучающий набор для обучения модели и проверочный набор для тестирования.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
Этот шаг гарантирует, что для проверки готовой модели будут использоваться точки данных, которые не использовались для обучения модели. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Подключение к рабочей области машинного обучения Azure
Класс **Workspace** в Машинном обучении Azure принимает сведения о подписке и ресурсах Azure. Он также создает облачный ресурс для мониторинга и отслеживания работы модели. На этом шаге мы создадим объект рабочей области на основе имеющейся рабочей области машинного обучения Azure.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Преобразование кадров данных в набор данных Машинного обучения Azure
Чтобы отправить удаленный эксперимент, нам необходимо преобразовать набор данных в формат ```TabularDatset``` для Машинного обучения Azure. [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) переводит данные в табличный формат, выполняя синтаксический анализ предоставленных файлов.

Приведенный ниже код позволяет получить сведения о существующей рабочей области и стандартном хранилище данных Машинного обучения Azure. Затем это хранилище и расположение файлов передаются в параметре path для создания нового ```TabularDataset```. 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![Изображение отправленного набора данных.](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automl-experiment"></a>Отправка эксперимента автоматизированного машинного обучения

#### <a name="define-training-settings"></a>Определение параметров обучения
1. Чтобы отправить эксперимент, нам нужно определить параметр эксперимента и свойства модели для обучения. Полный список этих свойств см. [здесь](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train).

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 10,
    "experiment_timeout_minutes": 30,
    "enable_early_stopping": True,
    "primary_metric": 'r2_score',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 2}
```

2. Теперь мы передадим параметры обучения, которые определили выше, в качестве параметра **kwargs в объект AutoMLConfig. Так как обучение выполняется в Spark, нужно передать еще и контекст Spark, который автоматически будет доступен в переменной ```sc```. Кроме того, мы укажем обучающие данные и тип модели (в нашем примере это регрессия).

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data = dataset_training,
                             spark_context = sc,
                             model_explainability = False, 
                             label_column_name ="fareAmount",**automl_settings)
```

> [!NOTE]
>Этапы предварительной обработки автоматизированного машинного обучения (нормализация компонентов, обработка недостающих данных, преобразование текста в числовой формат и т. д.) становятся частью базовой модели. При использовании модели для прогнозирования эти этапы предварительной обработки, которые выполнялись во время обучения, автоматически выполняются для входных данных.

#### <a name="train-the-automatic-regression-model"></a>Обучение модели автоматической регрессии 
Теперь мы создадим объект эксперимента в рабочей области машинного обучения Azure. Эксперимент выступает в качестве контейнера для отдельных запусков. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
После завершения эксперимента возвращаются выходные данные с информацией о завершенных итерациях. Для каждой итерации вы видите тип модели, длительность выполнения и точность обучения. Поле BEST отслеживает лучшую оценку текущего обучения, основанную на выбранном типе метрики.

![Снимок экрана с выходными данными модели.](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> После отправки эксперимент AutoML будет выполнять разные итерации с разными типами моделей. Процесс выполнения обычно занимает 1–1,5 ч. 

#### <a name="retrieve-the-best-model"></a>Извлечение наиболее эффективной модели
Чтобы выбрать лучшую модель из выполненных итераций, мы применим функцию ```get_output```, которая возвращает лучшее выполнение и полученную в нем модель. В приведенном ниже коде извлекается лучшее выполнение и соответствующая ему модель по любой зарегистрированной метрике или по определенной итерации.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>Проверка точности модели
1. Чтобы проверить точность модели, мы применим лучшую из полученных моделей для прогнозирования тарифов на такси по набору проверочных данных. Функция ```predict``` использует лучшую модель и прогнозирует значения Y (стоимость поездки) по набору проверочных данных. 

```python
# Test best model accuracy
validation_data_pd = validation_data.toPandas()
y_test = validation_data_pd.pop("fareAmount").to_frame()
y_predict = fitted_model.predict(validation_data_pd)
```

2. Для оценки разницы между прогнозируемыми и наблюдаемыми значениями часто используется величина "корень среднеквадратической погрешности" (root-mean-square error или RMSE). Мы вычислим корень среднеквадратической погрешности для полученных результатов, сравнив кадр данных y_test со значениями, прогнозируемыми моделью. 

   Функция ```mean_squared_error``` принимает два массива значений и вычисляет значение среднеквадратической погрешности между ними. Из полученного результата мы извлекаем квадратный корень. Эта метрика дает грубую оценку того, насколько далеки полученные прогнозы стоимости поездок на такси от фактических значений.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

# Calculate Root Mean Square Error
y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))

print("Root Mean Square Error:")
print(rmse)
```

```Output
Root Mean Square Error:
2.309997102577151
```
Корень среднеквадратичной погрешности хорошо оценивает точность прогнозов модели. Как вы видите, конечный результат демонстрирует хорошую эффективность нашей модели в прогнозировании стоимости поездок на такси по признакам из набора данных, обычно с точностью ±2,00 доллара США.

3. Чтобы вычислить среднюю абсолютную погрешность в процентах (mean absolute percent error или MAPE), выполните следующий код. Эта метрика выражает точность в процентах величины ошибки. Для этого она вычисляет абсолютное отклонение для каждой пары прогнозируемого и фактического значения, а затем суммирует все отклонения. Затем эта сумма выражается в процентах от общей суммы фактических значений.

```python
# Calculate MAPE and Model Accuracy 
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals

print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

```Output
Model MAPE:
0.03655071038487368

Model Accuracy:
0.9634492896151263
```
Обе метрики точности прогнозирования демонстрируют, что модель достаточно хороша в прогнозировании стоимости поездок на такси на основе признаков из набора данных. 

4. После подгонки модели линейной регрессии нам осталось лишь определить, насколько хорошо модель соответствует реальным данным. Для этого мы сопоставим графики фактических значений стоимости поездок и прогнозируемых данных. Кроме того, мы вычислим величину достоверности аппроксимации, чтобы оценить отклонение наших данных от линейной регрессии.

```python
import matplotlib.pyplot as plt
import numpy as np
from sklearn.metrics import mean_squared_error, r2_score

# Calculate the R2 score using the predicted and actual fare prices
y_test_actual = y_test["fareAmount"]
r2 = r2_score(y_test_actual, y_predict)

# Plot the Actual vs Predicted Fare Amount Values
plt.style.use('ggplot')
plt.figure(figsize=(10, 7))
plt.scatter(y_test_actual,y_predict)
plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
plt.xlabel("Actual Fare Amount")
plt.ylabel("Predicted Fare Amount")
plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
plt.show()

```
![Снимок экрана с графиком регрессии.](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   Из этих результатов мы видим, что величина достоверности аппроксимации составляет 95 % наблюдаемой дисперсии. Также это подтверждается сравнением графиков реальных и наблюдаемых значений. Чем большую часть дисперсии учитывает модель регрессии, тем ближе будут точки данных к графику полученной регрессии.  

## <a name="register-model-to-azure-machine-learning"></a>Регистрация модели в службе "Машинное обучение Azure"
Завершив проверку лучшей из полученных моделей, мы теперь можем зарегистрировать ее в Машинном обучении Azure. После регистрации вы сможете скачать или развернуть зарегистрированную модель, а также получить все зарегистрированные в ней файлы.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>Просмотр результатов в Машинном обучении Azure
Наконец, вы можете получить результаты конкретных итераций, перейдя к нужном эксперименту в рабочей области машинного обучения Azure. Здесь вы сможете подробно изучить дополнительные сведения о состоянии выполнения, проверенных моделях и других метриках модели. 

![Снимок экрана рабочей области AML.](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>Дальнейшие действия
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Учебник по Apache Spark MLlib](./apache-spark-machine-learning-mllib-notebook.md)