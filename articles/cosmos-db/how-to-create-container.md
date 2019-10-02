---
title: Создание контейнеров в Azure Cosmos DB
description: Узнайте, как создать контейнеры в Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 9805ff9aa4932c262db13c47fd2e442b3d3d676f
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811728"
---
# <a name="create-an-azure-cosmos-container"></a>Создание контейнера Azure Cosmos

Узнайте, как создавать контейнеры Azure Cosmos (коллекции, таблицы или графы). Для этого можно использовать портал Azure, Azure CLI или поддерживаемые пакеты SDK. В этой статье показано, как создать контейнер, указать ключ секции и подготовить пропускную способность.

## <a name="create-a-container-using-azure-portal"></a>Создание контейнера с помощью портала Azure

### <a id="portal-sql"></a>API SQL

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Azure Cosmos](create-sql-api-dotnet.md#create-account) или выберите существующую.

1. Откройте панель **Обозреватель данных** и выберите **создать контейнер**. После этого предоставьте следующие сведения.

   * Укажите, создаете ли вы новую базу данных или используете существующую.
   * Введите идентификатор контейнера.
   * Введите ключ секции.
   * Укажите подготавливаемую пропускную способность (например, 1000 ЕЗ/с).
   * Нажмите кнопку **ОК**.

    ![Снимок экрана панели обозреватель данных с выделенным новым контейнером](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API Azure Cosmos DB для MongoDB

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) или выберите существующую.

1. Откройте панель **Обозреватель данных** и выберите **создать контейнер**. После этого предоставьте следующие сведения.

   * Укажите, создаете ли вы новую базу данных или используете существующую.
   * Введите идентификатор контейнера.
   * Введите ключ сегмента.
   * Укажите подготавливаемую пропускную способность (например, 1000 ЕЗ/с).
   * Нажмите кнопку **ОК**.

    ![Снимок экрана: Azure Cosmos DB API для MongoDB, диалоговое окно "Добавление контейнера"](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API Cassandra

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account) или выберите существующую.

1. Откройте панель **Обозреватель данных** и выберите **New Table** (Новая таблица). После этого предоставьте следующие сведения.

   * Укажите, создаете ли вы новое пространство ключей или используете существующее.
   * Введите имя таблицы.
   * Укажите свойства и первичный ключ.
   * Укажите подготавливаемую пропускную способность (например, 1000 ЕЗ/с).
   * Нажмите кнопку **ОК**.

    ![Снимок экрана API Cassandra, диалоговое окно добавления таблицы](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Для API Cassandra первичный ключ используется в качестве ключа секции.

### <a id="portal-gremlin"></a>API Gremlin

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Azure Cosmos](create-graph-dotnet.md#create-a-database-account) или выберите существующую.

1. Откройте панель **Обозреватель данных** и выберите **New Graph** (Новый граф). После этого предоставьте следующие сведения.

   * Укажите, создаете ли вы новую базу данных или используете существующую.
   * Укажите идентификатор графа.
   * Выберите емкость **Без ограничений**.
   * Введите ключ секции для вершин.
   * Укажите подготавливаемую пропускную способность (например, 1000 ЕЗ/с).
   * Нажмите кнопку **ОК**.

    ![Снимок экрана API Gremlin, диалоговое окно добавления графа](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>API таблиц

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Azure Cosmos](create-table-dotnet.md#create-a-database-account) или выберите существующую.

1. Откройте панель **Обозреватель данных** и выберите **New Table** (Новая таблица). После этого предоставьте следующие сведения.

   * Укажите идентификатор таблицы.
   * Укажите подготавливаемую пропускную способность (например, 1000 ЕЗ/с).
   * Нажмите кнопку **ОК**.

    ![Снимок экрана API таблиц, диалоговое окно добавления таблицы](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Для API таблиц ключ секции указывается каждый раз при добавлении новой строки.

## Создание контейнера с помощью Azure CLI<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

В приведенных ниже ссылках показано, как создать ресурсы контейнера для Azure Cosmos DB с помощью Azure CLI.

Список всех Azure CLI примеров для всех Azure Cosmos DB API см. в разделе [API SQL](cli-samples.md), [API Cassandra](cli-samples-cassandra.md), [API MongoDB](cli-samples-mongodb.md), [Gremlin API](cli-samples-gremlin.md)и [API таблиц](cli-samples-table.md)

* [Создание контейнера с Azure CLI](manage-with-cli.md#create-a-container)
* [Создание коллекции для Azure Cosmos DB для API-интерфейса MongoDB с Azure CLI](/scripts/cli/mongodb/create.md)
* [Создание таблицы Cassandra с Azure CLI](/scripts/cli/cassandra/create.md)
* [Создание графа Gremlin с Azure CLI](/scripts/cli/gremlin/create.md)
* [Создание API таблиц таблицы с Azure CLI](/scripts/cli/table/create.md)

## Создание контейнера с помощью PowerShell<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

В приведенных ниже ссылках показано, как создать ресурсы контейнера для Azure Cosmos DB с помощью PowerShell.

Список всех Azure CLI примеров для всех Azure Cosmos DB API см. в разделе [API SQL](powershell-samples-sql.md), [API Cassandra](powershell-samples-cassandra.md), [API MongoDB](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md)и [API таблиц](powershell-samples-table.md)

* [Создание контейнера с помощью PowerShell](manage-with-powershell.md#create-container)
* [Создание коллекции для Azure Cosmos DB API MongoDB с помощью PowerShell](/scripts/powershell/mongodb/ps-mongodb-create.md)
* [Создание таблицы Cassandra с помощью PowerShell](/scripts/powershell/cassandra/ps-cassandra-create.md)
* [Создание графа Gremlin с помощью PowerShell](/scripts/powershell/gremlin/ps-gremlin-create.md)
* [Создание API таблиц таблицы с помощью PowerShell](/scripts/powershell/table/ps-table-create.md)

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
> Сетевой протокол MongoDB не поддерживает понятие [единицы запроса](request-units.md). Чтобы создать коллекцию с подготовленной пропускной способностью, используйте портал Azure или пакеты SDK Cosmos DB для API SQL.

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Следующие шаги

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Секционирование в Azure Cosmos DB)
* [Единицы запросов в Azure Cosmos DB](request-units.md)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Использование учетной записи Azure Cosmos](account-overview.md)
