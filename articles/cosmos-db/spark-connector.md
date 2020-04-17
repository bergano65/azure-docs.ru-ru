---
title: Подключение Apache Spark к Azure Cosmos DB
description: Сведения о соединителе Spark для Azure Cosmos DB, который позволяет подключать Apache Spark к Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: edfaf50b701f64b12f9cf5fcc9ab8d2c6d241d0a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482173"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Ускорение аналитики больших данных с помощью соединителя Apache Spark для Azure Cosmos DB

Вы можете запускать задания [Spark](https://spark.apache.org/) с данными, хранящимися в Azure Cosmos DB, с помощью разъема Cosmos DB Spark. Космос может использоваться для обработки пакетов и потоков, а также в качестве симного слоя для доступа с низкой задержкой.

Разъем можно использовать с [Azure Databricks](https://azure.microsoft.com/services/databricks) или [Azure HDInsight,](https://azure.microsoft.com/services/hdinsight/)которые предоставляют управляемые кластеры Spark в Azure. Следующая таблица показывает поддерживаемые версии Spark.

| Компонент | Версия |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x, и 2.1.x |
| Scala | 2.11 |
| Версия среды выполнения Azure Databricks | > 3.4 |

> [!WARNING]
> Этот разъем поддерживает базовый API Azure Cosmos DB.
> Для Cosmos DB для MongoDB API используйте [разъем MongoDB Spark.](https://docs.mongodb.com/spark-connector/master/)
> Для Cosmos DB Cassandra API используйте [разъем Cassandra Spark.](https://github.com/datastax/spark-cassandra-connector)
>

## <a name="quickstart"></a>Краткое руководство

* Выполните последующие действия в [Get started с Java SDK,](sql-api-async-java-get-started.md) чтобы настроить учетную запись Cosmos DB и заполнить некоторые данные.
* Выполните последующие действия в [Azure Databricks, которые начинают](/azure/azure-databricks/quickstart-create-databricks-workspace-portal) настраивать рабочее пространство и кластер Azure Databricks.
* Теперь можно создавать новые ноутбуки и импортировать библиотеку разъемов Cosmos DB. Перейти к [работе с разъемом Cosmos DB](#bk_working_with_connector) для получения подробной информации о том, как настроить рабочее пространство.
* В следующем разделе есть фрагменты о том, как читать и писать с помощью разъема.

### <a name="batch-reads-from-cosmos-db"></a>Пакет читает из Космос DB

Следующий фрагмент показывает, как создать Spark DataFrame для чтения из Космос DB в PySpark.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

И тот же фрагмент кода в Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Пакет пишет в Космос DB

Следующий фрагмент показывает, как написать рамку данных в Cosmos DB в PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

И тот же фрагмент кода в Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Потоковые чтения из Космос DB

Следующий фрагмент показывает, как подключиться к ленте изменения DB Azure Cosmos и читать его.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
И тот же фрагмент кода в Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Потоковая передача в Космос DB

Следующий фрагмент показывает, как написать рамку данных в Cosmos DB в PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

И тот же фрагмент кода в Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Больше фрагментов и от конца до конца образцы, [см.](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks)

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>Работа с разъемом

Вы можете построить разъем из источника в GitHub, или скачать Uber банки из Maven в ссылках ниже.

| Spark | Scala | Последняя версия |
|---|---|---|
| 2.4.0 | 2.11 | [лазурно-космосд-spark_2.4.0'2.11'1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [лазурно-космосдб-spark_2.3.0'2.11'1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [лазурно-космосдб-spark_2.2.0'2.11'1.1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [лазурно-космосдб-spark_2.1.0'2.11'1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Использование ноутбуков Databricks

Создайте библиотеку, используя рабочее пространство Databricks, следуя указаниям Azure Databricks > [используйте разъем Azure Cosmos DB Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Обратите внимание, что страница **«Использование разъема spark Spark Azure Cosmos DB»** в настоящее время не обновлена. Вместо загрузки шести отдельных банок в шесть различных библиотек, вы https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) можете скачать uber банку из Maven на и установить этот один банку / библиотеку.
> 

### <a name="using-spark-cli"></a>Использование искры-кли

Для работы с разъемом с помощью `spark-shell`искры-кли (то есть, `pyspark`, `spark-submit`), вы можете использовать `--packages` параметр с [координатами maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)разъема.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Использование ноутбуков Jupyter

Если вы используете ноутбуки Jupyter в HDInsight, вы `%%configure` можете использовать искру-магическую ячейку, чтобы указать координаты maven разъема.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Обратите внимание, что `spark.jars.excludes` включение этого значения является специфичным для устранения потенциальных конфликтов между разъемом, Apache Spark и Livy.

### <a name="build-the-connector"></a>Построить разъем

В настоящее время `maven` этот проект разъема использует так, чтобы построить без зависимостей, вы можете запустить:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Работа с нашими образцами

[В репозитории Cosmos DB Spark GitHub](https://github.com/Azure/azure-cosmosdb-spark) есть следующие образцы тетрадей и скриптов, которые вы можете попробовать.

* **На-время полета производительность с Spark и Космос DB (Сиэтл)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Подключите Spark к Космос DB с помощью HDInsight Jupyter ноутбук службы для демонстрации Spark S' L, GraphFrames, а также прогнозирования задержек рейсов с помощью ML трубопроводов.
* **Twitter Источник с Apache Spark и Azure Космос DB Изменение Feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Использование Apache Spark для запроса Cosmos DB Graphs:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Подключение Azure Databricks к Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** с помощью. `azure-cosmosdb-spark`  Связанная здесь версия Ноутбука [On-Time Performance Performance,](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)связанная с Azure Databricks.
* **[Архитектура Lambda с Azure Cosmos DB и HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Вы можете уменьшить эксплуатационные накладные расходы на поддержание конвейеров больших данных с помощью Cosmos DB и Spark.

## <a name="more-information"></a>Дополнительные сведения

У нас есть `azure-cosmosdb-spark` больше информации в [вики](https://github.com/Azure/azure-cosmosdb-spark/wiki) в том числе:

* [Azure Космос DB Разъем разъем разноборка Руководство](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Примеры агрегирования](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Настройка и установка

* [Конфигурация искры-коннектора](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Искра к космическому DB Коннектор Настройка](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (В процессе)
* [Настройка power BI Прямого запроса на Azure Cosmos DB через Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Устранение неполадок

* [Использование агрегатов Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Известные проблемы](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Производительность

* [Советы по производительности .NET](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Запуск ими тестов](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Написание тестовых запусков](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Канал изменений

* [Обработка потока изменяется с помощью канала Azure Cosmos DB И спарк Apache](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Изменение демо-версии ленты](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Структурированный поток Демонстрации](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Наблюдение

* [Мониторинг заданий Spark с помощью информации о приложениях](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Следующие шаги

Если у вас еще нет соединителя Spark для Azure Cosmos DB, скачайте его из репозитория GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark). Изучите следующие дополнительные ресурсы в репозитории:

* [Примеры агрегирований](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Образцы скриптов и тетрадей](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Дополнительные сведения см. в [руководстве по Apache Spark SQL, таблицам и наборам данных](https://spark.apache.org/docs/latest/sql-programming-guide.html), а также статье [Краткое руководство по созданию кластера Spark в HDInsight с помощью шаблона](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
