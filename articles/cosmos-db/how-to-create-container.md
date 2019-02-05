---
title: Создание контейнеров в Azure Cosmos DB
description: Узнайте, как создать контейнеры в Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 183f596c213f833a2ca633398d1102e86f328912
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468858"
---
# <a name="create-an-azure-cosmos-container"></a>Создание контейнера Azure Cosmos

В этой статье описываются различные способы создания контейнеров (коллекции, таблицы, диаграммы). Контейнер можно создать с помощью портала Azure, интерфейса командной строки Azure или поддерживаемых пакетов SDK. В этой статье показано, как создать контейнер, указать ключ секции и подготовить пропускную способность.

## <a name="create-a-container-using-azure-portal"></a>Создание контейнера с помощью портала Azure

### <a id="portal-sql"></a>API SQL

1. Войдите на [портал Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) или выберите имеющуюся.

1. Откройте панель **Обозреватель данных** и выберите **Новая коллекция**. Далее заполните форму следующими данными:

   * Создайте новую базу данных или используйте существующую.
   * Введите идентификатор коллекции.
   * Введите ключ секции.
   * Введите пропускную способность, например 1000 ЕЗ.
   * Нажмите кнопку **ОК**.

![SQL API создает коллекцию](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API Azure Cosmos DB для MongoDB

1. Войдите на [портал Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Cosmos DB](create-mongodb-dotnet.md#create-a-database-account) или выберите имеющуюся.

1. Откройте панель **Обозреватель данных** и выберите **Новая коллекция**. Далее заполните форму следующими данными:

   * Создайте новую базу данных или используйте существующую.
   * Введите идентификатор коллекции.
   * Выберите емкость **Без ограничений**.
   * Введите ключ сегмента.
   * Введите пропускную способность, например 1000 ЕЗ.
   * Нажмите кнопку **ОК**.

![Создание коллекции с использованием API Azure Cosmos DB для MongoDB](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API Cassandra

1. Войдите на [портал Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Cosmos DB](create-cassandra-dotnet.md#create-a-database-account) или выберите имеющуюся.

1. Откройте панель **Обозреватель данных** и выберите **Новая таблица**. Далее заполните форму следующими данными:

   * Создайте новое пространство ключей или используйте существующее.
   * Введите имя таблицы.
   * Укажите свойства и первичный ключ.
   * Введите пропускную способность, например 1000 ЕЗ.
   * Нажмите кнопку **ОК**.

![API Cassandra создает коллекцию](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Для API Cassandra первичный ключ используется в качестве ключа секции.

### <a id="portal-gremlin"></a>API Gremlin

1. Войдите на [портал Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Cosmos DB](create-graph-dotnet.md#create-a-database-account) или выберите имеющуюся.

1. Откройте панель **Обозреватель данных** и выберите **Новый граф**. Далее заполните форму следующими данными:

   * Создайте новую базу данных или используйте существующую.
   * Введите идентификатор графа.
   * Выберите емкость **Без ограничений**.
   * Введите ключ секции для вершин.
   * Введите пропускную способность, например 1000 ЕЗ.
   * Нажмите кнопку **ОК**.

![API Gremlin создает коллекцию](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>API таблиц

1. Войдите на [портал Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Cosmos DB](create-table-dotnet.md#create-a-database-account) или выберите имеющуюся.

1. Откройте панель **Обозреватель данных** и выберите **Новая таблица**. Далее заполните форму следующими данными:

   * Введите идентификатор таблицы.
   * Выберите емкость **Без ограничений**.
   * Введите пропускную способность, например 1000 ЕЗ.
   * Нажмите кнопку **ОК**.

![API таблиц создает коллекцию](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Для API таблиц ключ секции указывается каждый раз при добавлении новой строки.

## <a name="create-a-container-using-azure-cli"></a>Создание контейнера с помощью интерфейса командной строки Azure

### <a id="cli-sql"></a>API SQL

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>API Azure Cosmos DB для MongoDB

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>API Cassandra

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>API Gremlin

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>API таблиц

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-using-net-sdk"></a>Создайте контейнер с помощью .NET SDK

### <a id="dotnet-sql-graph"></a>API SQL и API Gremlin

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>API Azure Cosmos DB для MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
Сетевой протокол MongoDB не поддерживает понятие единицы запросов. Чтобы создать новую коллекцию с пропускной способностью, используйте портал Azure или API SQL, как показано в предыдущих примерах.

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать о секционировании в Cosmos DB, ознакомьтесь со следующими статьями:

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Секционирование в Azure Cosmos DB)
