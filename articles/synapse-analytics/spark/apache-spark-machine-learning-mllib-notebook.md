---
title: Создайте приложение для машинного обучения с помощью Apache Spark MLlib и Azure Synapse Analytics
description: Узнайте, как использовать Apache Spark MLlib для создания приложения для машинного обучения, которое анализирует набор данных с помощью классификации с помощью логистической регрессии.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430010"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Создайте приложение для машинного обучения с помощью Apache Spark MLlib и Azure Synapse Analytics

В этой статье вы узнаете, как использовать Apache Spark [MLlib](https://spark.apache.org/mllib/) для создания приложения для машинного обучения, которое делает простой прогностический анализ на открытом наборе данных Azure. Spark предоставляет встроенные библиотеки машинного обучения. В этом примере используется *классификация* с помощью логистической регрессии.

MLlib является основной библиотекой Spark, которая предоставляет множество утилит, которые полезны для задач машинного обучения, включая утилиты, подходящие для:

- Классификация
- Регрессия
- Кластеризация
- тематического моделирования;
- сингулярного разложения и анализа по методу главных компонент;
- проверки гипотез и статистической выборки.

## <a name="understand-classification-and-logistic-regression"></a>Общие сведения о классификации и логистической регрессии

*Классификация* — это распространенная задача машинного обучения, которая представляет собой процесс сортировки входных данных по категориям. Это задача алгоритма классификации, чтобы выяснить, как назначить *метки* для ввода данных, которые вы предоставляете. Например, можно подумать об алгоритме машинного обучения, который принимает информацию о запасах в качестве ввода и делит акции на две категории: акции, которые вы должны продать, и акции, которые вы должны хранить.

*Логистическая регрессия* — это алгоритм, который можно использовать для классификации. API Spark для логистической регрессии подходит для задач *двоичной классификации* или разделения входных данных на две группы. Дополнительные сведения о логистической регрессии см. в статье [Википедии](https://en.wikipedia.org/wiki/Logistic_regression).

В общих словах, в основе логистической регрессии лежит некая *логистическая функция* , используемая для прогнозирования вероятности принадлежности вектора входных данных к одной или другой группе.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Пример прогнозного анализа данных nyc Taxi

В этом примере вы используете Spark для выполнения прогностического анализа данных о наконечнике такси из Нью-йорка. Данные доступны через [открытые наборы данных Azure.](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) Этот подмножество набора данных содержит информацию о поездках на желтом такси, включая информацию о каждой поездке, времени начала и местоположениях, стоимости и других интересных атрибутах.

> [!IMPORTANT]
> За вытягивание этих данных из места хранения могут взиматься дополнительные сборы.

В следующих шагах вы разрабатываете модель, чтобы предсказать, включает ли та или иная поездка чаевые или нет.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Создание приложения машинного обучения Apache Spark MLlib

1. Создайте блокнот с помощью ядра PySpark. Для инструкций [см.](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook)
2. Импортируйте типы, необходимые для этого приложения. Копируйте и вставьте следующий код в пустую ячейку, а затем нажмите **SHIFT и ENTER**, или запустите ячейку с помощью синего значка воспроизведения слева от кода.

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

    Ядро PySpark позволяет не задавать контексты явным образом. Контекст Spark автоматически создается для вас при запуске первой ячейки кода.

## <a name="construct-the-input-dataframe"></a>Создание входного кадра данных

Поскольку исходные данные в формате Паркета, можно использовать контекст Spark, чтобы вытащить файл в память в качестве непосредственного кадра данных. В то время как приведенный ниже код использует параметры по умолчанию, при необходимости можно принудить к отображению типов данных и других атрибутов схемы.

1. Выполнить следующие строки для создания кадра данных Spark, вставив код в новую ячейку. Первый раздел присваивает переменным информацию о доступе к хранилищу Azure. Второй раздел позволяет Spark считывать с помощью кабл-хранилища удаленно. Последняя строка кода считывает паркет, но на данный момент данные не загружаются.

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

2. Вытягивание всех этих данных генерирует около 1,5 миллиардов строк. В зависимости от размера пула Spark (предварительный просмотр) исходные данные могут быть слишком большими или слишком много времени для работы. Вы можете отфильтровать эти данные до чего-то меньшего. При необходимости добавьте следующие строки для фильтрации данных до примерно 2 миллионов строк для более гибкого интерфейса. Используйте эти параметры, чтобы вытащить одну неделю данных.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. Недостатком простой фильтрации является то, что, с статистической точки зрения, она может привести к смещению данных. Другой подход заключается в использовании выборки, встроенной в Spark. Следующий код уменьшает набор данных до примерно 2000 строк, если он применяется после приведенного выше кода. Этот шаг выборки можно использовать вместо простого фильтра или в сочетании с простым фильтром.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Теперь можно посмотреть на данные, чтобы увидеть, что было прочитано. Обычно лучше просматривать данные с подмножеством, а не полный набор в зависимости от размера набора данных. Следующий код предлагает два способа просмотра данных: первый является базовым, а второй обеспечивает гораздо более богатый опыт сетки, а также возможность визуализировать данные графически.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. В зависимости от размера генерируемого размера набора данных и необходимости многократного эксперимента или запуска ноутбука может возникнуть целесообразно кэшировать набор данных в рабочей области. Существует три способа выполнения явного кэширования:

   - Сохранение локального кадра данных в виде файла
   - Сохранение кадра данных в качестве временной таблицы или представления
   - Сохранить фрейм данных в качестве постоянной таблицы

Первые 2 из этих подходов включены в следующие примеры кода.

Создание таблицы временных топоров или представления обеспечивает различные пути доступа к данным, но длится только в течение сеанса экземпляра Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Используемые данные

Обычно вы будете проходить через этап *исследовательского анализа данных* (EDA) на данный момент, чтобы развить понимание данных. Следующий код показывает три различных визуализации данных, связанных с советами, которые приводят к выводам о состоянии и качестве данных.

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

![](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![Гистограмма Box](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![Уwhisker Участок Рассеянный Участок](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Подготовка данных

Данные в сыром виде часто не подходят для передачи непосредственно модели. На данных необходимо выполнить ряд действий, чтобы довести их до состояния, в котором модель может их использовать.

В коде ниже четырех классов операций выполняются:

- Удаление выбросов/неправильных значений через фильтрацию.
- Удаление столбцов, которые не нужны.
- Создание новых столбцов, полученных из необработанных данных, чтобы сделать модель работать более эффективно, иногда называют феатуризацией.
- Маркировка, как вы проводите двоичной классификации (будет ли отзыв или нет на данной поездки) есть необходимость конвертировать кончик сумму в 0 или 1 значение.

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

Затем над данными производится второй проход для добавления окончательных функций.

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

И последняя задача — преобразуйте данные с метками в такой формат, который удобен для анализа методом логистической регрессии. Вход в алгоритм логистической регрессии должен быть набором *векторных пар меток-функция,* где *вектор функции* является вектором чисел, представляющих точку ввода. Итак, нам нужно преобразовать категоричные столбцы в числа. `trafficTimeBins` Колонны `weekdayString` и столбцы должны быть преобразованы в несколько представлений. Существует несколько подходов к выполнению преобразования, однако подход, принятый в этом примере, является *OneHotEncoding*, общий подход.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Это приводит к новому фрейму данных со всеми столбцов в правильном формате для обучения модели.

## <a name="train-a-logistic-regression-model"></a>Поезд логистической модели регрессии

Первая задача состоит в том, чтобы разделить набор данных на учебный набор и набор тестирования или проверки. Раскол здесь является произвольным, и вы должны играть вокруг с различными настройками разделения, чтобы увидеть, если они влияют на модель.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Теперь, когда есть два DataFrames, следующая задача состоит в том, чтобы создать формулу модели и запустить ее против обучения DataFrame, а затем проверить против тестирования DataFrame. Следует экспериментировать с различными версиями формулы модели, чтобы увидеть влияние различных комбинаций.

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

Выход из этой ячейки

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Создание визуального представления прогноза

Теперь создайте итоговую визуализацию, которая позволит оценить результаты теста. [Кривая ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) является одним из способов анализа результата.

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

![Кривая ROC для модели наконечника логистической регрессии](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "Кривая ROC для модели наконечника логистической регрессии")

## <a name="shut-down-the-spark-instance"></a>Выключите экземпляр Spark

После того как вы закончили работу приложения, выключите блокнот, чтобы освободить ресурсы, закрыв вкладку или выберите **End Session** из панели статуса в нижней части ноутбука.

## <a name="see-also"></a>См. также раздел

- [Обзор: Apache Искра на Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Дальнейшие действия

- [.NET для документации Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Официальная документация Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Часть официальной документации Apache Spark основывается на использовании консоли Spark, которая недоступна на Azure Synapse Spark. Вместо этого используйте [блокнот](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) или опыт [IntelliJ.](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
