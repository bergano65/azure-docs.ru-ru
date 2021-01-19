---
title: Краткое руководство. Начало анализа с помощью Spark
description: Из этого руководства вы узнаете, как анализировать данные с помощью Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 3b5f5d64498922e9fc35942ff4570d801aa6c516
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118886"
---
# <a name="analyze-with-apache-spark"></a>Анализ с помощью Apache Spark

В этом учебнике описываются основные шаги по загрузке и анализу данных с помощью Apache Spark для Azure Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Анализ данных такси Нью-Йорка в Хранилище BLOB-объектов с помощью Spark

1. В центре **Данные** выберите элемент **Добавить новый ресурс** (кнопка со знаком плюса над пунктом **Связанный**) > **Обзор коллекции**.
1. Выберите элемент **NYC Taxi & Limousine Commission - yellow taxi trip records**.
1. В нижней части страницы выберите элементы **Продолжить** > **Добавить набор данных**.
1. В центре **Данные** в разделе **Связанный** щелкните правой кнопкой мыши элемент **Хранилище BLOB-объектов Azure** и выберите элементы **Sample Datasets (Примеры наборов данных)**  > **nyc_tlc_yellow**
1. Выберите элемент **Создать записную книжку**, чтобы создать записную книжку со следующим кодом:

    ```py
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```

1. В записной книжке в меню **Присоединить к** выберите бессерверный пул Spark.
1. В ячейке выберите элемент **Выполнить**.
1. Если вы хотите просмотреть только схему кадра данных, выполните ячейку со следующим кодом:

    ```py
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Загрузка данных нью-йоркского такси в базу данных Spark "nyctaxi"

Данные доступны в таблице в пуле **SQLPOOL1**. Загрузите их в базу данных Spark с именем **nyctaxi**.

1. В Synapse Studio перейдите в центр **Разработка**.
1. Выберите **+**  > **Записная книжка**.
1. В верхней части записной книжки задайте для параметра **Присоединить к** значение **Spark1**.
1. Выберите элемент **Добавить код**, чтобы добавить ячейку кода записной книжки, и вставьте следующий текст:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. В ячейке выберите элемент **Выполнить**.
1. В центре **Данные** щелкните правой кнопкой мыши элемент **Базы данных** и выберите команду **Обновить**. Вы должны видеть такие базы данных:

    - **SQLPOOL1 (SQL)** ;
    - **nyctaxi (Spark)** .

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Анализ данных нью-йоркского такси с помощью Spark и записных книжек

1. Вернитесь к своей записной книжке.
1. Создайте новую ячейку кода и введите следующий текст.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Выполните ячейку, чтобы отобразить данные о такси Нью-Йорка, которые вы загрузили в базу данных Spark **nyctaxi**.
1. Выполните приведенный ниже код, чтобы провести тот же анализ, который вы реализовали ранее с помощью выделенного пула SQL **SQLPOOL1**. Этот код сохраняет результаты анализа в таблицу с именем **nyctaxi.passengercountstats** и выводит эти результаты.

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

Ранее вы скопировали данные из таблицы выделенного пула SQL **SQLPOOL1.dbo.Trip** в таблицу Spark **nyctaxi.trip**. Затем вы агрегировали данные в таблицу Spark **nyctaxi.passengercountstats**. Теперь скопируйте данные из **nyctaxi.passengercountstats** в таблицу выделенного пула SQL с именем **SQLPOOL1.dbo.PassengerCountStats**.

Запустите следующую ячейку в записной книжке. Сводная таблица Spark копируется обратно в таблицу выделенного пула SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Анализ данных с помощью бессерверного пула SQL](get-started-analyze-sql-on-demand.md)
