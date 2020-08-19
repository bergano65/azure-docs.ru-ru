---
title: Выполнение экспериментов с помощью автоматического машинного обучения Azure
description: Запуск экспериментов машинного обучения с помощью Apache Spark и автоматического машинного обучения Azure
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: 57830ae14c5d6653f6a2225924160fcb75646c12
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590766"
---
# <a name="run-experiments-using-azure-automated-ml-and-apache-spark"></a>Выполнение экспериментов с помощью автоматического машинного обучения Azure и Apache Spark

Машинное обучение Azure — это облачная среда, которая позволяет обучать, развертывать, автоматизировать и контролировать модели машинного обучения. 

В этом руководстве вы используете [автоматизированное машинное обучение](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) в машинное обучение Azure, чтобы создать регрессионную модель для прогнозирования Нью такси FARE. Этот процесс принимает обучающие данные и параметры конфигурации и автоматически выполняет итерации по сочетаниям методов нормализации и стандартизации функций, моделей и параметров настройки параметров, которые можно приступать к лучшей модели.

В этом руководстве описано, как выполнять такие задачи:
- Скачивание данных с помощью Apache Spark и открытых наборов данных Azure
- Преобразование и очистка данных с помощью Apache Spark кадров данных
- Обучение автоматизированной регрессионной модели машинного обучения.
- Расчет точности модели.

### <a name="before-you-begin"></a>Перед началом
- Создайте пул Apache Spark, следуя [учебнику создание пула Apache Spark](../quickstart-create-apache-spark-pool-studio.md).
- Заполните [учебник по настройке рабочей области машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) , если у вас нет существующей рабочей области машинное обучение Azure. 

### <a name="understand-regression-models"></a>Общие сведения о моделях регрессии
*Модели регрессии* прогнозирует числовые выходные значения на основе независимых прогностические факторы. В случае регрессии цель заключается в том, чтобы установить связь между этими независимыми переменными прогнозирования, оценивая, как одна переменная влияет на другие.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>Пример регрессионного анализа данных такси Нью
В этом примере вы будете использовать Spark для анализа данных Совета по поездкам в такси из Нью Йорк. Данные доступны через [открытые наборы данных Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Это подмножество набора данных содержит сведения о поездках на такси, включая сведения о каждой поездке, времени начала и окончания и расположениях, стоимости и других интересных атрибутах.

> [!IMPORTANT]
> 
> За получение этих данных из хранилища может взиматься дополнительная плата. В следующих шагах предстоит разработать модель для прогнозирования НЬЮов такси FARE. 
> 

##  <a name="download-and-prepare-the-data"></a>Загрузка и подготовка данных

1. Создайте записную книжку, используя ядро PySpark. Инструкции см. [в разделе Создание записной книжки](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.) .
   
   > [!Note]
   > 
   > Ядро PySpark позволяет не задавать контексты явным образом. Контекст Spark будет создан автоматически при выполнении первой ячейки кода.
   >

2. Поскольку необработанные данные имеют формат Parquet, можно использовать контекст Spark, чтобы извлечь файл в память напрямую в виде таблицы данных. Создайте таблицу данных Spark, извлекая данные через API Open DataSets. Здесь мы будем использовать схему "кадры данных Spark" *для чтения* свойств, чтобы определить типы данных и схему. 
   
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

3. В зависимости от размера пула Spark (предварительная версия), необработанные данные могут быть слишком большими или занимать слишком много времени для обработки. Эти данные можно отфильтровать по меньшему объему с ```start_date``` помощью ```end_date``` фильтров и. При этом применяется фильтр, возвращающий данные месяца. После получения отфильтрованной таблицы данных мы также выполним ```describe()``` функцию на новом кадре данных, чтобы просмотреть сводную статистику для каждого поля. 

   На основе сводной статистики можно увидеть, что в данных есть некоторое количество отклонения и выбросов. Например, статистика показывает, что минимальное расстояние поездок меньше 0. Нам нужно будет отфильтровать эти несоответствующие точки данных.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Теперь мы будем создавать функции из набора данных, выбирая набор столбцов и создавая различные функции на основе времени из поля Дата отправки. Мы также отфильтрим выбросы, обнаруженные на предыдущем шаге, а затем удалим несколько последних столбцов, которые не нужны для обучения.
   
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
   
Как видите, в этом случае создается новая таблица данных с дополнительными столбцами для дня месяца, часа отправки, дня недели и общего времени поездки. 

![Изображение таблицы данных такси.](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>Создание наборов данных тестирования и проверки

После получения нашего последнего набора данных можно разделить данные на обучающие и проверочные наборы с помощью ```random_ split ``` функции Spark. Используя указанные весовые коэффициенты, эта функция случайным образом разделяет данные на обучающий набор данных для обучения модели и проверочный набор данных для тестирования.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

Этот шаг гарантирует, что точки данных проверяют готовую модель, которая не использовалась для обучения модели. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Подключение к рабочая область машинного обучения Azure
В Машинное обучение Azure  **Рабочая область** — это класс, который принимает сведения о подписке и ресурсах Azure. Он также создает облачный ресурс для мониторинга и отслеживания работы модели. На этом шаге мы создадим объект рабочей области из существующей рабочей области Машинное обучение Azure.
   
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

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Преобразование кадров данных в Машинное обучение Azure
Чтобы отправить удаленный эксперимент, необходимо преобразовать набор данных в Машинное обучение Azure ```TabularDatset``` . [Табулардатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) представляет данные в табличном формате путем синтаксического анализа предоставленных файлов.

Следующий код возвращает существующую рабочую область и стандартное хранилище данных по умолчанию Машинное обучение Azure. Затем он передает хранилище данных и расположения файлов в параметр path, чтобы создать новый объект ```TabularDataset``` . 

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

![Изображение отправленного набора данных.](./media/apache-spark-machine-learning-aml-notebook/upload-dataset.png)

## <a name="submit-an-auto-ml-experiment"></a>Отправка эксперимента автоматического обучения

#### <a name="define-training-settings"></a>Определение параметров обучения

1. Чтобы отправить эксперимент, необходимо определить параметр эксперимента и параметры модели для обучения. Полный список параметров можно просмотреть [здесь](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train).

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

2. Теперь мы передаем определенные параметры обучения в качестве \* \* параметра кваргс в объект аутомлконфиг. Так как мы обучены в Spark, мы также должны передать контекст Spark, который автоматически становится доступным для ```sc``` переменной. Кроме того, в этом случае мы будем указывать обучающие данные и тип модели, которая является регрессией.

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
> Этапы предварительной обработки автоматизированного машинного обучения (нормализация компонентов, обработка недостающих данных, преобразование текста в числовой формат и т. д.) становятся частью базовой модели. При использовании модели для прогнозирования эти этапы предварительной обработки, которые выполнялись во время обучения, автоматически выполняются для входных данных.

#### <a name="train-the-automatic-regression-model"></a>Обучение модели автоматической регрессии 
Теперь мы создадим объект эксперимента в рабочей области Машинное обучение Azure. Эксперимент выступает в качестве контейнера для отдельных запусков. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

После завершения эксперимента выходные данные будут возвращать сведения о завершенных итерациях. Для каждой итерации вы видите тип модели, длительность выполнения и точность обучения. В поле лучше отслеживать лучшие показатели обучения на основе типа метрики.

![Снимок экрана вывода модели.](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> После отправки Аутомл эксперимент будет выполнять различные итерации и типы моделей. Этот запуск обычно займет 1 – 1,5 часа. 

#### <a name="retrieve-the-best-model"></a>Извлечение наиболее эффективной модели
Чтобы выбрать наилучшую модель из итераций, мы будем использовать ```get_output``` функцию для возврата наилучшей модели выполнения и помещается. Приведенный ниже код извлечет наиболее подходящую модель выполнения и занесения для любой зарегистрированной в журнале метрики или определенной итерации.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>Проверка точности модели

1. Чтобы проверить точность модели, мы будем использовать наилучшую модель для выполнения прогнозов такси FARE в наборе проверочных данных. ```predict```Функция использует наилучшую модель и прогнозирует значения y (FARE Amount) из проверочного набора данных. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2. Ошибка с корнем среднего квадрата (корень СРЕДНЕКВАДРАТИЧНОЙ ПОГРЕШНОСТИ) — это часто используемая мера различий между примерами значений, прогнозируемых моделью и наблюдаемыми значениями. Мы вычислим среднюю квадратную ошибку результатов, сравнив y_test кадр данных с значениями, прогнозируемыми моделью. 

   Функция ```mean_squared_error``` принимает два массива и вычисляет среднюю квадратную ошибку между ними. Затем мы принимаем квадратный корень результата. Эта метрика указывает на то, насколько прогноз FARE такси из фактических значений году.

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
   
   Ошибка «root-среднее-квадрат» — это хороший показатель того, насколько точная модель прогнозирует ответ. Из результатов вы увидите, что модель достаточно хорошо подходит для прогнозирования такси году из функций набора данных, обычно в пределах +-$2,00

3. Выполните следующий код, чтобы вычислить среднюю абсолютную ошибку процента (MAPE). Эта метрика выражает точность в процентах от ошибки. Для этого вычисляется абсолютное различие между прогнозируемыми и фактическими значениями, а затем суммируются все различия. Затем она выражает эту сумму как процент от итога фактических значений.

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
   Из двух метрик точности прогнозов видно, что модель достаточно хорошо подходит для прогнозирования такси году из функций набора данных. 

4. После подгонки модели линейной регрессии теперь необходимо определить, насколько хорошо модель будет подсчитывать данные. Для этого мы будем отображать фактические значения FARE для прогнозируемого выхода. Кроме того, мы будем рассчитать меру R-квадраты, чтобы понять, как близки данные к линейной регрессии.

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
   
   ![Снимок экрана: регрессионная диаграмма.](./media/apache-spark-machine-learning-aml-notebook/aml-fare-amount.png)

   Из результатов мы видим, что учетные записи мер R-квадраты для 95% нашей дисперсии. Это также проверяется фактическими обратными метками. Чем больше вариативность, которая учитывается моделью регрессии, тем ближе точки данных будут попадать в линейку с заданной регрессией.  

## <a name="register-model-to-azure-machine-learning"></a>Регистрация модели для Машинное обучение Azure
После проверки лучшей модели можно зарегистрировать модель для Машинное обучение Azure. После регистрации модели можно скачать или развернуть зарегистрированную модель и получить все зарегистрированные файлы.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>Просмотр результатов в Машинное обучение Azure
Наконец, можно также получить доступ к результатам итераций, перейдя к эксперименту в рабочая область машинного обучения Azure. Здесь вы сможете подробно ознакомиться с дополнительными сведениями о состоянии выполнения, попыток моделей и других метриках модели. 

![Снимок экрана рабочей области AML.](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>Дальнейшие действия
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Руководство по Apache Spark MLlib](./apache-spark-machine-learning-mllib-notebook.md)
