---
title: Краткое руководство. Начало анализа с помощью Spark
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
ms.openlocfilehash: d7b198790b1ecc884321ad42c97eb5cf0c239b7e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241987"
---
# <a name="analyze-with-apache-spark"></a>Анализ с помощью Apache Spark

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Анализ данных такси Нью-Йорка в Хранилище BLOB-объектов с помощью Spark

В этом учебнике описываются основные шаги по загрузке и анализу данных с помощью Apache Spark для Azure Synapse.

1. В центре **Данные** щелкните **Добавить новый ресурс** (кнопка со знаком плюса над пунктом **Связанный**) >> **Обзор**. 
1. Найдите и выберите **NYC Taxi & Limousine Commission - yellow taxi trip records**. 
1. В нижней части страницы щелкните **Продолжить** и выберите **Добавить набор данных**. 
1. Теперь в центре **Данные** в разделе **Связанный** щелкните правой кнопкой мыши элементы **Хранилище BLOB-объектов Azure >> Sample Datasets (Примеры наборов данных) >> nyc_tlc_yellow** и выберите элемент **Новая записная книжка**.
1. Будет создана записная книжка со следующим кодом:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. В записной книжке в меню **Присоединить к** выберите бессерверный пул Spark.
1. В ячейке выберите элемент **Выполнить**.
1. Если вы просто хотите просмотреть схему кадра данных, выполните ячейку со следующим кодом:
    ```
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Загрузка данных нью-йоркского такси в базу данных Spark "nyctaxi"

В таблице есть данные, доступные в **SQLPOOL1**. Загрузите их в базу данных Spark с именем **nyctaxi**.

1. В Synapse Studio перейдите в центр **Разработка**.
1. Выберите **+**  > **Записная книжка**.
1. В верхней части записной книжки задайте для параметра **Присоединить к** значение **Spark1**.
1. Выберите **Добавить код**, для того, чтобы добавить ячейку кода записной книжки, и вставьте следующий текст:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Перейдите в центр **Данные**, щелкните правой кнопкой мыши **Базы данных** и потом выберите **Обновить**. Вы должны видеть такие базы данных:

    - **SQLPOOL1 (SQL)** ;
    - **nyctaxi (Spark)** .

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Анализ данных нью-йоркского такси с помощью Spark и записных книжек

1. Вернитесь к своей записной книжке.
1. Создайте новую ячейку кода и введите следующий текст. Потом запустите ячейку, чтобы показать данные про нью-йоркское такси, которые мы загрузили в базу данных Spark **nyctaxi**.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Выполните приведенный ниже код, чтобы провести тот же анализ, который был реализован ранее с помощью выделенного пула SQL **SQLPOOL1**. Этот код сохраняет результаты анализа в таблицу с именем **nyctaxi.passengercountstats** и визуализирует результаты.

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





## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Загрузка данных из таблицы Spark в таблицу выделенного пула SQL

Ранее мы скопировали данные из таблицы выделенного пула SQL **SQLPOOL1.dbo.Trip** в таблицу Spark **nyctaxi.trip**. Затем, используя Spark, данные были агрегированы в таблицу Spark **nyctaxi.passengercountstats**. Теперь скопируйте данные из **nyctaxi.passengercountstats** в таблицу выделенного пула SQL с именем **SQLPOOL1.dbo.PassengerCountStats**.

Запустите следующую ячейку в записной книжке. Сводная таблица Spark копируется обратно в таблицу выделенного пула SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Анализ данных с помощью бессерверного пула SQL](get-started-analyze-sql-on-demand.md)


