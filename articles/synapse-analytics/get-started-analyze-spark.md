---
title: Учебник. Приступая к анализу с помощью Spark
description: В этом руководстве изложены основные этапы настройки и использования Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5c6b35c1d9f00cae8fc688569e3a491679900995
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093802"
---
# <a name="analyze-with-apache-spark"></a>Анализ с помощью Apache Spark

В этом учебнике описываются основные шаги по загрузке и анализу данных с помощью Apache Spark для Azure Synapse.

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
> [Анализ данных в службе хранилища](get-started-analyze-storage.md)


