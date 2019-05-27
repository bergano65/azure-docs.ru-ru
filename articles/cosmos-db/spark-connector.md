---
title: Подключение Apache Spark к Azure Cosmos DB
description: Сведения о соединителе Spark для Azure Cosmos DB, который позволяет подключать Apache Spark к Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: bc0f2044f70c674177f9c9786f56f0441db2e282
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978907"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Ускорение аналитики больших данных с помощью соединителя Apache Spark для Azure Cosmos DB

Можно запустить [Spark](https://spark.apache.org/) заданий с данными, хранящимися в Azure Cosmos DB с помощью соединителя Spark для Cosmos DB. Cosmos может использоваться для пакетной обработки и потоковой обработки, а также как уровень обслуживания для высокоскоростной доступ.

Вы можете использовать соединитель с [Azure Databricks](https://azure.microsoft.com/services/databricks) или [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), которые предоставляют управляемые кластеры Spark в Azure. В следующей таблице показаны поддерживаемые Spark версии.

| Компонент | Version |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x и 2.1.x |
| Scala | 2.11 |
| Версия среды выполнения Azure Databricks | > 3.4 |

> [!WARNING]
> Этот соединитель поддерживает core (SQL) API Azure Cosmos DB.
> Cosmos DB для MongoDB API, используйте [соединитель MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Cosmos DB Cassandra API, используйте [соединитель Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Быстрый старт

* Следуйте инструкциям из статьи [приступить к работе с пакетом SDK для Java](sql-api-async-java-get-started.md) для настройки учетной записи Cosmos DB и заполняется данными.
* Следуйте инструкциям из статьи [Приступая к работе в Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) для настройки рабочей области Azure Databricks и кластера.
* Теперь можно создать новые записные книжки, а затем импортировать в библиотеку соединителя Cosmos DB. Перейти к [работа с соединитель Azure Cosmos DB](#bk_working_with_connector) Дополнительные сведения о настройке рабочей области.
* Следующий раздел содержит фрагменты кода о том, как для чтения и записи, с помощью соединителя.

### <a name="reading-from-cosmos-db"></a>Чтение из Cosmos DB

Следующий фрагмент кода показано, как создать таблицу данных Spark для чтения из Cosmos DB в PySpark.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

И фрагмент кода на языке Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
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

### <a name="writing-to-cosmos-db"></a>Записи в Cosmos DB

Следующий фрагмент кода показано, как записать кадр данных в Cosmos DB в PySpark.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

И фрагмент кода на языке Scala:

```scala
// Configure connection to the sink collection
val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Upsert the dataframe to Cosmos DB
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Для получения дополнительных фрагментов и полноценные примеры кода, см. в разделе [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a> Работа с соединителем

Можно создать соединитель из источника в Github или скачайте uber JAR-файлы из Maven по ссылкам ниже.

| Spark | Scala | Последняя версия |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.3.5](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>С помощью записных книжек в Databricks

Создать библиотеку с помощью рабочей области Databricks, следуя указаниям в руководстве по Azure Databricks > [использовать соединитель Spark для Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Обратите внимание, что **используйте соединитель Azure Cosmos DB Spark** страница в настоящее время не является актуальной. Вместо загрузки шести отдельных JAR-файлов в шести различных библиотеках, jar-файл можно загрузить из maven в https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar) и установить этот один JAR-файл или библиотеку.
> 

### <a name="using-spark-cli"></a>С помощью spark-cli

Для работы с соединителем, с помощью spark-cli (то есть `spark-shell`, `pyspark`, `spark-submit`), можно использовать `--packages` параметра с помощью соединителя [координаты maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5"

```

### <a name="using-jupyter-notebooks"></a>Использование записных книжек Jupyter

Если вы используете записных книжек Jupyter в HDInsight, можно использовать волшебной команды spark `%%configure` ячейку, чтобы задать координаты maven соединителя.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Обратите внимание, включение `spark.jars.excludes` относится только к удаления потенциальных конфликтов между соединителем, Apache Spark и Livy.

### <a name="build-the-connector"></a>Создание соединителя

В настоящее время этот соединитель проект использует `maven` , чтобы выполнить сборку без зависимостей, который можно запустить:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Работа с нашими примерами

[Репозиторий GitHub Spark для Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark) имеет следующие примеры записных книжек и скриптов, которые вы можете повторить.

* **О прибытии по графику рейсов с помощью Spark и Cosmos DB (Сиэтл)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Подключение Spark к Cosmos DB с помощью службы HDInsight Jupyter notebook для демонстрации Spark SQL, GraphFrames и прогнозирующих задержки авиарейсов, с помощью конвейеров машинного Обучения.
* **[Подключение Spark с помощью веб-канала изменений Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark%2Band%2BCosmos%2BDB%2BChange%2BFeed.ipynb)**: Быстрый showcase о том, как подключить Spark к Cosmos DB веб-канала изменений.
* **Twitter источника с Apache Spark и Azure Cosmos DB веб-канала изменений**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Использование Apache Spark для Cosmos DB графы запроса**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Подключение Azure Databricks к Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  с помощью `azure-cosmosdb-spark`.  Связанная здесь также является версией Azure Databricks [записной книжки о прибытии по графику рейсов](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Лямбда-архитектуры с помощью Azure Cosmos DB и HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Можно снизить операционные затраты на обслуживание конвейеров больших данных с помощью Cosmos DB и Spark.

## <a name="more-information"></a>Дополнительные сведения

У нас есть Дополнительные сведения `azure-cosmosdb-spark` [вики-сайте](https://github.com/Azure/azure-cosmosdb-spark/wiki) включая:

* [Руководство пользователя соединителя Spark в Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Примеры статистических выражений](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Настройка и установка

* [Конфигурация соединителя Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark для настройки соединителя Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (выполняется)
* [Настройка Power BI Direct Query в Azure Cosmos DB с помощью Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Устранение неполадок

* [С помощью Cosmos DB агрегатов](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Известные проблемы](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Производительность

* [Советы по повышению производительности](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Запрос тестовых запусков](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Создание тестовых запусков](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Канал изменений

* [Stream обработки изменений с использованием канала изменений Azure Cosmos DB и Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Демонстрационные веб-канала изменений](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Демонстрации структурированных Stream](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Мониторинг

* [Отслеживание заданий Spark с помощью application insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Дальнейшие действия

Если у вас еще нет соединителя Spark для Azure Cosmos DB, скачайте его из репозитория GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark). Изучите следующие дополнительные ресурсы в репозитории:

* [Примеры агрегирований](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Примеры скриптов и записных книжек](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Дополнительные сведения см. в [руководстве по Apache Spark SQL, таблицам и наборам данных](https://spark.apache.org/docs/latest/sql-programming-guide.html), а также статье [Краткое руководство по созданию кластера Spark в HDInsight с помощью шаблона](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
