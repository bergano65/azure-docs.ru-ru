---
title: Подготовка пропускной способности контейнера в Azure Cosmos DB
description: Узнайте о том, как подготовить пропускную способность на уровне контейнера в Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: dd47976bca75569142f1912eee06c66061e92fa6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097671"
---
# <a name="provision-throughput-for-an-azure-cosmos-db-container"></a>Подготовка пропускной способности для коллекций Azure Cosmos DB

В этой статье описывается, как подготовить пропускную способность для контейнера (коллекции, диаграммы, таблицы) в Azure Cosmos DB. Можно подготовить пропускную способность для одного контейнера или [для базы данных](how-to-provision-database-throughput.md) и предоставить к ней общий доступ между контейнерами. Пропускную способность контейнера можно подготовить с помощью портала Azure, интерфейса командной строки Azure или пакетов SDK CosmosDB.

## <a name="provision-throughput-using-azure-portal"></a>Подготовка пропускной способности с помощью портала Azure

1. Войдите на [портал Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) или выберите имеющуюся.

1. Откройте панель **Обозреватель данных** и выберите **Новая коллекция**. Далее заполните форму следующими данными:

   * Создайте новую базу данных или используйте существующую.
   * Введите идентификатор коллекции (или таблицы, графика).
   * Введите значение ключа раздела, например `/userid`.
   * Введите пропускную способность, например 1000 ЕЗ.
   * Нажмите кнопку **ОК**.

![Пропускная способность контейнера API SQL](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Подготовка пропускной способности с помощью портала Azure CLI

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

При подготовке пропускной способности для учетной записи API MongoDB используйте /myShardKey для пути к ключу раздела и при подготовке пропускной способности для учетной записи API Cassandra используйте /myPrimaryKey для пути к ключу секции.

## <a name="provision-throughput-using-net-sdk"></a>Подготовка пропускной способности с помощью пакета SDK для .NET

> [!Note]
> Используйте SQL API для подготовки пропускной способности для всех API, за исключением API Cassandra.

### <a id="dotnet-most"></a>Интерфейсы API SQL, MongoDB, Gremlin и API таблиц

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать о подготовке пропускной способности в Cosmos DB, обратитесь к следующим статьям:

* [Provision throughput for a database in Azure Cosmos DB](how-to-provision-database-throughput.md) (Подготовка пропускной способности для базы данных в Azure Cosmos DB)
* [Пропускная способность и единицы запросов в Azure Cosmos DB](request-units.md)
