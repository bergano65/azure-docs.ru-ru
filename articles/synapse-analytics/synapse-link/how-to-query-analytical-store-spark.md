---
title: Отправка запросов в аналитическое хранилище Azure Cosmos DB с помощью Apache Spark для Azure Synapse Analytics
description: Практическое руководство по отправке запросов в аналитическое хранилище Azure Cosmos DB с помощью Apache Spark для Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 02d4b6a636bff5ef11686abba6efb52f45f04779
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599256"
---
# <a name="query-azure-cosmos-db-analytical-store-with-apache-spark-for-azure-synapse-analytics"></a>Отправка запросов в аналитическое хранилище Azure Cosmos DB с помощью Apache Spark для Azure Synapse Analytics

В этой статье приводится ряд примеров того, как можно взаимодействовать с аналитическим хранилищем с помощью жестов Synapse. Чтобы просмотреть жесты, щелкните правой кнопкой мыши контейнер. С помощью жестов можно быстро создать код и скорректировать его в соответствии с потребностями. Жесты также идеально подходят для обнаружения данных одним щелчком мыши.

## <a name="load-to-dataframe"></a>Загрузка в DataFrame

На этом шаге вы считаете данные из аналитического хранилища Azure Cosmos DB в Spark DataFrame. В результате будут отображены 10 строк из DataFrame под названием ***DF***. После переноса данных в DataFrame можно выполнить дополнительный анализ.

Эта операция не влияет на хранилище транзакций.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

Эквивалентным жестом кода в **Scala** будет следующий:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Создание таблицы Spark

В этом жесте вы создадите таблицу Spark, указывающую на выбранный контейнер. Эта операция не влечет за собой перемещение данных. Если вы решили удалить эту таблицу, это никак не повлияет на базовый контейнер (и соответствующее аналитическое хранилище). 

Этот сценарий удобен для повторного использования таблиц с применением средств сторонних разработчиков и предоставления доступа к данным в среде выполнения.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>Запись DataFrame в контейнер

В этом жесте вы запишете DataFrame в контейнер. Эта операция влияет на производительность при выполнении транзакций и использование единиц запросов. Использование производительности Azure Cosmos DB для выполнения транзакций идеально подходит для транзакций записи. Не забудьте заменить **YOURDATAFRAME** объектом DataFrame, в который нужно осуществить обратную запись.

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Эквивалентным жестом кода в **Scala** будет следующий:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Загрузка DataFrame для потоковой передачи из контейнера
В этом жесте вы будете использовать возможность потоковой передачи Spark для загрузки данных из контейнера в DataFrame. Данные будут храниться в основной учетной записи Data Lake (и файловой системе), подключенной к рабочей области. 

Если папка */localReadCheckpointFolder* отсутствует, она будет создана автоматически. Эта операция влияет на производительность при выполнении транзакций в Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Эквивалентным жестом кода в **Scala** будет следующий:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>Запись DataFrame для потоковой передачи в контейнер
В этом жесте вы запишете объект DataFrame потоковой передачи в выбранный контейнер Azure Cosmos DB. Если папка */localReadCheckpointFolder* отсутствует, она будет создана автоматически. Эта операция влияет на производительность при выполнении транзакций в Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

Эквивалентным жестом кода в **Scala** будет следующий:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте, какие общие возможности поддерживают Synapse и Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md).
* [Подключитесь к Synapse Link для Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md).
