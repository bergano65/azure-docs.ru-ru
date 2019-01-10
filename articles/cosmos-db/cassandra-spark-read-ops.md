---
title: Чтения табличных данных API Cassandra с помощью Spark
titleSufix: Azure Cosmos DB
description: В этой статье рассказывается, как читать данные из таблиц API Cassandra в Azure Cosmos DB.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 01a9582062d8eb0d039473a03901fc83fe179020
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033423"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Чтения данных из таблиц API Cassandra в Azure Cosmos DB с помощью Spark

 В этой статье рассказывается, как читать данные, сохраненные в API Cassandra для Azure Cosmos DB, из Spark.

## <a name="cassandra-api-configuration"></a>Конфигурация API Cassandra
```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>API Dataframe

### <a name="read-table-using-sessionreadformat-command"></a>Чтение таблицы с помощью команды session.read.format

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Чтение таблицы, с помощью spark.read.cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Чтение определенных столбцов в таблице

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Применение фильтров

В настоящее время включение предиката не поддерживается; приведенные ниже примеры демонстрируют фильтрацию на стороне клиента. 

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")
  .filter("book_pub_year > 1891")
//.filter("book_name IN ('A sign of four','A study in scarlet')")
//.filter("book_name='A sign of four' OR book_name='A study in scarlet'")
//.filter("book_author='Arthur Conan Doyle' AND book_pub_year=1890")
//.filter("book_pub_year=1903")  

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

## <a name="rdd-api"></a>API RRD

### <a name="read-table"></a>Чтение таблицы
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Чтение определенных столбцов в таблице

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>Представления SQL 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Создание временного представления из кадра данных

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Выполнение запросов к представлению

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные статьи по работе с API Cassandra для Azure Cosmos DB из Spark:
 
 * [Операции upsert](cassandra-spark-upsert-ops.md)
 * [Операции удаления](cassandra-spark-delete-ops.md)
 * [Операции агрегирования](cassandra-spark-aggregation-ops.md)
 * [Операции копирования таблиц](cassandra-spark-table-copy-ops.md)

