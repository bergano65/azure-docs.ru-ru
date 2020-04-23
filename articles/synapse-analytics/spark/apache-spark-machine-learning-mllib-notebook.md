---
title: Создание приложения машинного обучения с помощью Apache Spark MLlib и Azure синапсе Analytics
description: Узнайте, как использовать Apache Spark MLlib для создания приложения машинного обучения, которое анализирует набор данных с помощью классификации посредством логистической регрессии.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 25d11d2cf41f8653c5a54007f121c1251bb24b1f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096305"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Создание приложения машинного обучения с помощью Apache Spark MLlib и Azure синапсе Analytics

Из этой статьи вы узнаете, как использовать Apache Spark [MLlib](https://spark.apache.org/mllib/) для создания приложения машинного обучения, которое выполняет простой прогнозный анализ в открытом наборе данных Azure. Spark предоставляет встроенные библиотеки машинного обучения. В этом примере *классификация* используется с помощью логистической регрессии.

MLlib — это основная библиотека Spark, которая предоставляет множество служебных программ, полезных для задач машинного обучения, включая служебные программы, которые подходят для:

- Классификация
- Регрессия
- Кластеризация
- тематического моделирования;
- сингулярного разложения и анализа по методу главных компонент;
- проверки гипотез и статистической выборки.

## <a name="understand-classification-and-logistic-regression"></a>Общие сведения о классификации и логистической регрессии

*Классификация* — это распространенная задача машинного обучения, которая представляет собой процесс сортировки входных данных по категориям. Это задание алгоритма классификации, позволяющее выяснить, как назначать *метки* входным данным, которые вы предоставляете. Например, можно представить алгоритм машинного обучения, который принимает в качестве входных данных биржевую информацию и разделяет их на две категории: акции, которые следует продавать и акции, которые следует хранить.

*Логистическая регрессия* — это алгоритм, который можно использовать для классификации. API Spark для логистической регрессии подходит для задач *двоичной классификации* или разделения входных данных на две группы. Дополнительные сведения о логистической регрессии см. в статье [Википедии](https://en.wikipedia.org/wiki/Logistic_regression).

В общих словах, в основе логистической регрессии лежит некая *логистическая функция* , используемая для прогнозирования вероятности принадлежности вектора входных данных к одной или другой группе.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Пример прогнозного анализа данных Нью такси

В этом примере Spark используется для выполнения прогнозного анализа данных о поездках на основе такси из Нью Йорк. Данные доступны через [открытые наборы данных Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Это подмножество набора данных содержит сведения о желтых поездках в такси, включая сведения о каждом пути, времени начала и окончания и расположениях, стоимости и других интересных атрибутах.

> [!IMPORTANT]
> За извлечение этих данных из своего места хранения могут взиматься дополнительные платежи.

На следующих шагах вы разрабатываете модель для прогнозирования того, содержит ли конкретный поездку Совет.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Создание приложения машинного обучения Apache Spark MLlib

1. Создайте записную книжку с помощью ядра PySpark. Инструкции см. в разделе [Создание записной книжки](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Импортируйте типы, необходимые для этого приложения. Скопируйте и вставьте следующий код в пустую ячейку, а затем нажмите клавиши **SHIFT + ВВОД**или выполните ячейку с помощью синего значка воспроизведения слева от кода.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Ядро PySpark позволяет не задавать контексты явным образом. Контекст Spark создается автоматически при выполнении первой ячейки кода.

## <a name="construct-the-input-dataframe"></a>Создание входного кадра данных

Поскольку необработанные данные имеют формат Parquet, можно использовать контекст Spark, чтобы извлечь файл в память в виде таблицы данных напрямую. Хотя в приведенном ниже коде используются параметры по умолчанию, при необходимости можно принудительно сопоставить типы данных и другие атрибуты схемы.

1. Выполните следующие строки, чтобы создать таблицу данных Spark, вставляя код в новую ячейку. В первом разделе сведения о доступе к хранилищу Azure назначаются переменным. Второй раздел позволяет удаленно читать Spark из хранилища BLOB-объектов. Последняя строка кода считывает Parquet, но в этот момент данные не загружаются.

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. Извлечение всех этих данных приводит к 1 500 000 000 строк. В зависимости от размера пула Spark (Предварительная версия) необработанные данные могут быть слишком большими или занимать слишком много времени для работы. Эти данные можно отфильтровать по меньшей мере. При необходимости добавьте следующие строки, чтобы отфильтровать данные примерно в 2 000 000 строк для более быстрого реагирования. Используйте эти параметры, чтобы извлечь данные за неделю.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. Недостаток простой фильтрации состоит в том, что с точки зрения статистической схемы она может привести к смещению данных. Другой подход заключается в использовании выборки, встроенной в Spark. Следующий код сокращает набор данных до 2000 строк, если применяется после приведенного выше кода. Этот шаг выборки можно использовать вместо простого фильтра или в сочетании с простым фильтром.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Теперь можно просмотреть данные, чтобы узнать, что было считано. Обычно лучше рассмотреть данные с подмножеством, а не с полным набором, в зависимости от размера набора данных. В следующем коде представлено два способа просмотра данных: первый из них является базовым, а второй предоставляет более широкие возможности сетки, а также возможность визуализировать данные графически.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. В зависимости от размера созданного размера набора данных и необходимости поэкспериментировать или запустить записную книжку может быть целесообразно кэшировать набор данных локально в рабочей области. Существует три способа выполнения явного кэширования.

   - Сохранение кадра данных локально в виде файла
   - Сохранить кадр данных как временную таблицу или представление
   - Сохранение кадра данных в виде постоянной таблицы

Первые два из этих подходов включены в следующие примеры кода.

Создание временной таблицы или представления предоставляет разные пути доступа к данным, но продолжается только в течение сеанса экземпляра Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Используемые данные

Обычно на этом этапе вы просматриваете этап *анализа исследовательских данных* (EDA), чтобы разрабатывать представление о данных. В следующем коде показаны три различные визуализации данных, связанные с советами, которые ведут к выводам сведений о состоянии и качества данных.

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![Гистограмма](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![усами график](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![точечной диаграммы](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Подготовка данных

Данные в их необработанной форме часто не подходят для передачи непосредственно в модель. Для данных необходимо выполнить ряд действий, чтобы получить состояние, в котором модель может ее использовать.

В коде, приведенном ниже, выполняются четыре класса операций:

- Удаление выбросов и неверных значений с помощью фильтрации.
- Удаление ненужных столбцов.
- Создание новых столбцов, производных от необработанных данных, для повышения эффективности работы модели, иногда называемой Добавление признаков.
- Добавление меток, так как вы принимаете двоичную классификацию (будет существовать Совет или не в данном пути), необходимо преобразовать значение Tip в 0 или 1.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Затем выполняется второй проход по данным для добавления окончательных функций.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>создание модели логистической регрессии;

И последняя задача — преобразуйте данные с метками в такой формат, который удобен для анализа методом логистической регрессии. Входными данными для алгоритма логистической регрессии должен быть набор *пар «метка-вектор*», где *вектор* признаков — это вектор чисел, представляющий точку входа. Поэтому необходимо преобразовать столбцы категорий в числа. Столбцы `trafficTimeBins` и `weekdayString` должны быть преобразованы в целочисленные представления. Существует несколько подходов к выполнению преобразования, однако в этом примере используется *онехотенкодинг*, общий подход.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Это приводит к новому кадру данных со всеми столбцами в правильном формате для обучения модели.

## <a name="train-a-logistic-regression-model"></a>Обучение модели логистической регрессии

Первая задача — разделить набор данных на обучающий набор и проверочный или проверочный набор. Разбиение здесь является произвольным, и вы должны поэкспериментировать с различными параметрами разбиения, чтобы определить, влияют ли они на модель.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Теперь, когда имеются две таблицы данных, необходимо создать формулу модели и запустить ее для обучающей таблицы данных, а затем выполнить проверку по тестовому кадру данных. Следует поэкспериментировать с различными версиями формулы модели, чтобы увидеть влияние различных сочетаний.

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

Выходные данные этой ячейки:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Создание визуального представления прогноза

Теперь создайте итоговую визуализацию, которая позволит оценить результаты теста. [ROCная кривая](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) — это один из способов просмотра результата.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![ROC кривая для модели логистического TIP](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "ROC кривая для модели логистического TIP")

## <a name="shut-down-the-spark-instance"></a>Завершение работы экземпляра Spark

Завершив работу с приложением, завершите работу записной книжки, чтобы освободить ресурсы, закрыв вкладку или выбрав пункт **завершить сеанс** на панели состояния в нижней части записной книжки.

## <a name="see-also"></a>См. также раздел

- [Обзор: Apache Spark в Azure синапсе Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Документация по .NET для Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Официальная документация по Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Некоторые из официальных документов по Apache Spark предполагают использование консоли Spark, которая недоступна в Spark для Azure Synapse. Вместо этого используйте интерфейсы [записной книжки](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) или [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
