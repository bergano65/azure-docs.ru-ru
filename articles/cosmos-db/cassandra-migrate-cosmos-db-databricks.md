---
title: Перенос данных из Apache Cassandra в Azure Cosmos DB API Cassandra с использованием кирпичей данных (Spark)
description: Узнайте, как перенести данные из базы данных Apache Cassandra в Azure Cosmos DB API Cassandra с помощью Azure Databricks и Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 877aa96b189de47d158721df6585cb94ace4a855
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94932918"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Перенос данных из Cassandra в учетную запись API Cassandra Azure Cosmos DB с помощью Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API Cassandra в Azure Cosmos DB стали отличным выбором для корпоративных рабочих нагрузок, работающих на Apache Cassandra, по различным причинам: 

* **Отсутствие дополнительных издержек при управлении и мониторинге:** Она устраняет издержки на управление множеством параметров в файлах ОС, ВИРТУАЛЬНОЙ машины Java и YAML и их взаимодействиях.

* **Значительные сокращения затрат:** Вы можете сэкономить затраты с Azure Cosmos DB, включая стоимость виртуальных машин, пропускной способности и применимых лицензий. Кроме того, вам не нужно управлять центрами обработки данных, серверами, хранилищем SSD, сетью и расходами электроэнергии. 

* **Возможность использования существующего кода и средств:** Azure Cosmos DB обеспечивает совместимость на уровне кабельных протоколов с существующими пакетами SDK и средствами Cassandra. Благодаря этой совместимости можно использовать имеющуюся базу кода с API Cassandra для Azure Cosmos DB, внеся простые изменения.

Существует несколько способов миграции рабочих нагрузок базы данных с одной платформы на другую. [Azure Databricks](https://azure.microsoft.com/services/databricks/) — это платформа в качестве предложения услуги для [Apache Spark](https://spark.apache.org/) , которая предлагает способ выполнения автономных миграций в крупном масштабе. В этой статье описываются шаги, необходимые для переноса данных из собственных пространств ключей и таблиц Apache Cassandra в Azure Cosmos DB API Cassandra с помощью Azure Databricks.

## <a name="prerequisites"></a>Предварительные требования

* [Предоставление учетной записи API Cassandra для Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Просмотр основных сведений о подключении к API Cassandra для Azure Cosmos DB](cassandra-spark-generic.md)

* Чтобы обеспечить совместимость, ознакомьтесь с [поддерживаемыми функциями Azure Cosmos DB API Cassandra](cassandra-support.md) .

* Убедитесь, что вы уже создали пустые пространства ключей и таблицы в целевой Azure Cosmos DB учетной записи API Cassandra

* [Использование cqlsh или размещенной оболочки для проверки](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Предоставление кластера Azure Databricks

Вы можете выполнить инструкции по [подготовке кластера Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Однако обратите внимание, Apache Spark 3. x в настоящее время не поддерживается для соединителя Apache Cassandra. Необходимо подготавливать среду выполнения для модуля данных с поддерживаемой версией версии 2. x Apache Spark. Мы рекомендуем использовать среду выполнения для модуля данных версии 6,6:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Среда выполнения модулями":::


## <a name="add-dependencies"></a>Добавление зависимостей

Чтобы подключиться как к собственным, так и Cosmos DBным конечным точкам Cassandra, необходимо добавить в кластер библиотеку соединителей Apache Spark Cassandra. В кластере выберите библиотеки. > установить новые > пакеты поиска Maven->:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Пакеты поиска в модулях":::

Введите `Cassandra` в поле поиска и выберите `spark-cassandra-connector` доступный последний репозиторий Maven, а затем нажмите кнопку установить.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Выбор пакетов в модулях":::

> [!NOTE]
> Убедитесь, что кластер кирпичей перезапускается после установки библиотеки соединителей Cassandra.

## <a name="create-scala-notebook-for-migration"></a>Создание записной книжки Scala для миграции

Создайте записную книжку Scala в модулях, используя следующий код. Замените исходные и целевые конфигурации Cassandra соответствующими учетными данными, исходными и целевыми пространств ключей и таблицами, а затем выполните:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> `spark.cassandra.output.concurrent.writes`Конфигурации и `connections_per_executor_max` важны для предотвращения [ограничения скорости](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/), что происходит, когда запросы Cosmos DB превышают подготовленную пропускную способность ([единицы запроса](https://docs.microsoft.com/azure/cosmos-db/request-units)). Может потребоваться изменить эти параметры в зависимости от количества исполнителей в кластере Spark и, возможно, размера (и, следовательно, стоимости на ЕДИНИЦу) каждой записи, записываемой в целевые таблицы.

## <a name="next-steps"></a>Следующие шаги

* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md) 
* [Рекомендации по использованию ключа секции](partitioning-overview.md#choose-partitionkey)
* [Оценка единиц запросов в секунду с помощью планировщика ресурсов Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
* [Эластичное масштабирование в Azure Cosmos DB API Cassandra](manage-scale-cassandra.md)
