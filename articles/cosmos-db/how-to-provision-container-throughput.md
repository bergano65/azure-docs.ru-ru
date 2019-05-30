---
title: Подготовка пропускной способности контейнера в Azure Cosmos DB
description: Узнайте о том, как подготовить пропускную способность на уровне контейнера в Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: dec6d0a65e556e0572bd030617e5b2c30078fce8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243834"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Подготовка пропускной способности для контейнера Azure Cosmos

Узнайте, как подготовить пропускную способность для контейнера (коллекции, графа или таблицы) в Azure Cosmos DB. Вы можете подготовить пропускную способность для одного контейнера или [базы данных](how-to-provision-database-throughput.md) и предоставить к ней общий доступ для контейнеров в пределах этой базы данных. Пропускную способность для контейнера можно подготовить с помощью портала Azure, Azure CLI или пакетов SDK Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Подготовка пропускной способности с помощью портала Azure

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Azure Cosmos](create-sql-api-dotnet.md#create-account) или выберите существующую.

1. Откройте панель **Обозреватель данных** и выберите **Новая коллекция**. После этого предоставьте следующие сведения.

   * Укажите, создаете ли вы новую базу данных или используете существующую.
   * Укажите идентификатор контейнера (таблицы или графа).
   * Введите значение ключа секции (например, `/userid`).
   * Укажите подготавливаемую пропускную способность (например, 1000 ЕЗ/с).
   * Нажмите кнопку **ОК**.

![Снимок экрана обозревателя данных с выделенным элементом "Новая коллекция"](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

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

При подготовке пропускной способности для учетной записи Azure Cosmos, настроенной с помощью API Azure Cosmos DB для MongoDB, используйте `/myShardKey` для пути к ключу секции. При подготовке пропускной способности для контейнера в учетной записи Azure Cosmos, настроенной с помощью API Cassandra, используйте `/myPrimaryKey` для пути к ключу секции.

## <a name="provision-throughput-by-using-net-sdk"></a>Подготовка пропускной способности с помощью пакета SDK для .NET

> [!Note]
> Используйте пакеты SDK Cosmos для API SQL для подготовки пропускной способности для всех API Cosmos DB, кроме API Cassandra.

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

* [Подготовка пропускной способности для базы данных](how-to-provision-database-throughput.md)
* [Пропускная способность и единицы запросов в Azure Cosmos DB](request-units.md)
