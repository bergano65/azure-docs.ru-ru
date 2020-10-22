---
title: Учебник. Приступая к анализу с помощью Spark
description: Из этого учебника вы узнаете, как анализировать данные с помощью Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: ec6af7c23f781d25114794066a228adbfe7528d0
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093622"
---
# <a name="analyze-with-apache-spark"></a>Анализ с помощью Apache Spark

## <a name="analyze-nyc-taxi-data-in-blob-storage--using-spark"></a>Анализ данных такси Нью-Йорка в Хранилище BLOB-объектов с помощью Spark

В этом учебнике описываются основные шаги по загрузке и анализу данных с помощью Apache Spark для Azure Synapse.

1. В центре **Данные** щелкните **Добавить новый ресурс** (кнопка со знаком плюса над пунктом **Связанный**) >> **Обзор**. Найдите и выберите **NYC Taxi & Limousine Commission - yellow taxi trip records**. В нижней части страницы щелкните **Продолжить** и выберите **Добавить набор данных**. Теперь в центре **Данные** в разделе **Связанный** щелкните правой кнопкой мыши **Хранилище BLOB-объектов Azure >> Примеры наборов данных >> nyc_tlc_yellow** и выберите **Новая записная книжка**.
1. Будет создана записная книжка со следующим кодом:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. В записной книжке в меню **Присоединить к** выберите пул Spark.
1. В ячейке щелкните **Выполнить**.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Загрузка данных нью-йоркского такси в базу данных Spark "nyctaxi"

В таблице есть данные, доступные в **SQLDB1**. Загрузите их в базу данных Spark с именем **nyctaxi**.

1. В Synapse Studio перейдите в центр **Разработка**.
1. Выберите **+**  > **Записная книжка**.
1. В верхней части записной книжки задайте для параметра **Присоединить к** значение **Spark1**.
1. Выберите **Добавить код**, для того, чтобы добавить ячейку кода записной книжки, и вставьте следующий текст:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Перейдите в центр **Данные**, щелкните правой кнопкой мыши **Базы данных** и потом выберите **Обновить**. Вы должны видеть такие базы данных:

    - **SQLDB1** (пул SQL)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Анализ данных нью-йоркского такси с помощью Spark и записных книжек

1. Вернитесь к своей записной книжке.
1. Создайте новую ячейку кода и введите следующий текст. Потом запустите ячейку, чтобы показать данные про нью-йоркское такси, которые мы загрузили в базу данных Spark **nyctaxi**.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Выполните приведенный ниже код, чтобы провести тот же анализ, который был сделан ранее с помощью пула SQL **SQLDB1**. Этот код сохраняет результаты анализа в таблицу с именем **nyctaxi.passengercountstats** и визуализирует результаты.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. В результатах ячейки выберите пункт **Диаграмма**, чтобы просмотреть визуализацию данных.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Настройка данных визуализации с помощью Spark и записных книжек

Вы можете управлять отображением диаграмм используя записные книжки. Ниже приведен простой пример кода. Он использует популярные библиотеки **matplotlib** и **seaborn**. Код отображает такой же вид графика, что и запросы SQL, которые мы выполняли ранее.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Загрузка данных из таблицы Spark в таблицу пула SQL

Ранее мы скопировали данные из таблицы пула SQL **SQLDB1. dbo.Trip** в таблицу Spark **nyctaxi.trip**. Затем, используя Spark, данные были агрегированы в таблицу Spark **nyctaxi.passengercountstats**. Теперь скопируйте данные из **nyctaxi.passengercountstats** в таблицу пула SQL с именем **SQLDB1.dbo.PassengerCountStats**.

Запустите следующую ячейку в записной книжке. Сводная таблица Spark копируется обратно в таблицу пула SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Анализ данных с помощью SQL по запросу](get-started-analyze-sql-on-demand.md)


