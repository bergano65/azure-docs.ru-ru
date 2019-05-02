---
title: Определение расходов в единицах запроса (ЕЗ) в Azure Cosmos DB
description: Узнайте, как определить стоимость в единицах запроса для любой операции, выполняемой в контейнере Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: thweiss
ms.openlocfilehash: 7afa815f81e2a61db8ac83623baafb97cb986b2c
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925358"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>Определение расходов в единицах запроса (ЕЗ) в Azure Cosmos DB

В этой статье описано несколько способов, позволяющих определить потребление [единиц запроса](request-units.md) для любой операции, которая выполняется в контейнере Azure Cosmos. Сейчас потребление можно оценить с помощью портала Azure или сведений из ответа, который Azure Cosmos DB возвращает через пакеты SDK.

## <a name="sql-core-api"></a>API SQL (Core)

### <a name="use-the-azure-portal"></a>Использование портала Azure

Сейчас портал Azure позволяет узнать стоимость запроса только для SQL-запросов.

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте учетную запись Azure Cosmos](create-sql-api-dotnet.md#create-account) и заполните ее данными или выберите существующую учетную запись Azure Cosmos, которая уже содержит данные.

1. Откройте панель **обозревателя данных** и выберите контейнер, с которым собираетесь работать.

1. Щелкните **Создать SQL-запрос**.

1. Введите допустимый запрос и щелкните **Выполнить запрос**.

1. Щелкните **Query Stats** (Статистика запросов), чтобы отобразилось количество единиц запроса, фактически затраченных на только что выполненный запрос.

![Снимок экрана со сведениями о стоимости запроса SQL на портале Azure](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Использование пакета SDK для .NET версии 2

Объекты, возвращаемые из [пакета SDK для .NET версии 2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (его применение описано в [этом кратком руководстве](create-sql-api-dotnet.md)) предоставляют свойство `RequestCharge`.

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="use-the-java-sdk"></a>Использование пакета SDK для Java

Объекты, возвращаемые из [пакета SDK для Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (его применение описано в [этом кратком руководстве](create-sql-api-java.md)) предоставляют метод `getRequestCharge()`.

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

### <a name="use-the-nodejs-sdk"></a>Использование пакета SDK для Node.js

Объекты, возвращаемые из [пакета SDK для Node.js](https://www.npmjs.com/package/@azure/cosmos) (его применение описано в [этом кратком руководстве](create-sql-api-nodejs.md)) предоставляют вложенный объект `headers`, который позволяет сопоставить все заголовки, возвращаемые базовым интерфейсом API для HTTP. Сведения о стоимости запроса доступны в ключе `x-ms-request-charge`.

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

### <a name="use-the-python-sdk"></a>Использование пакета SDK для Python

Объект `CosmosClient`, возвращаемый из [пакета SDK для Python](https://pypi.org/project/azure-cosmos/) (его применение описано в [этом кратком руководстве](create-sql-api-python.md)) предоставляют словарь `last_response_headers`, который позволяет сопоставить все заголовки для последней выполненной операции, возвращаемые базовым интерфейсом API для HTTP. Сведения о стоимости запроса доступны в ключе `x-ms-request-charge`.

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API Azure Cosmos DB для MongoDB

Расходы в единицах запроса предоставляются пользовательской [командой базы данных](https://docs.mongodb.com/manual/reference/command/) с именем `getLastRequestStatistics.`. Эта команда возвращает документ, содержащий имя последней выполненной операции, ее длительность и количество затраченных единиц запроса.

### <a name="use-the-azure-portal"></a>Использование портала Azure

Сейчас портал Azure позволяет узнать стоимость только для запросов.

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте учетную запись Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) и заполните ее данными или выберите существующую учетную запись, которая уже содержит данные.

1. Откройте панель **обозревателя данных** и выберите коллекцию, с которой собираетесь работать.

1. Щелкните **Создать запрос**.

1. Введите допустимый запрос и щелкните **Выполнить запрос**.

1. Щелкните **Query Stats** (Статистика запросов), чтобы отобразилось количество единиц запроса, фактически затраченных на только что выполненный запрос.

![Снимок экрана со сведениями о стоимости запроса MongoDB на портале Azure](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Использование драйвера .NET для MongoDB

Если вы используете [официальный драйвер .NET для MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/) (его применение описано в [этом кратком руководстве](create-mongodb-dotnet.md)), команды можно выполнять, вызвав метод `RunCommand` для объекта `IMongoDatabase`. Для работы этого метода необходимо реализовать абстрактный класс `Command<>`.

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

### <a name="use-the-mongodb-java-driver"></a>Использование драйвера Java для MongoDB

Если вы используете [официальный драйвер Java для MongoDB](https://mongodb.github.io/mongo-java-driver/) (его применение описано в [этом кратком руководстве](create-mongodb-java.md)), команды можно выполнять, вызвав метод `runCommand` для объекта `MongoDatabase`.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>Использование драйвера Node.js для MongoDB

Если вы используете [официальный драйвер Node.js для MongoDB](https://mongodb.github.io/node-mongodb-native/) (его применение описано в [этом кратком руководстве](create-mongodb-nodejs.md)), команды можно выполнять, вызвав метод `command` для объекта `db`.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>API Cassandra

Если вы выполняете операции с API Cassandra для Azure Cosmos DB, сведения о расходах в единицах запроса возвращаются в составе входящих полезных данных в поле с именем `RequestCharge`.

### <a name="use-the-net-sdk"></a>Использование пакета SDK для .NET

Если вы используете [пакет SDK для .NET](https://www.nuget.org/packages/CassandraCSharpDriver/) (его применение описано в [этом кратком руководстве](create-cassandra-dotnet.md)), входные полезные данные можно получить в свойстве `Info` объекта `RowSet`.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>Использование пакета SDK для Java

Если вы используете [пакет SDK для Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) (его применение описано в [этом кратком руководстве](create-cassandra-java.md)), входные полезные данные можно получить, вызвав метод `getExecutionInfo()` для объекта `ResultSet`.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>API Gremlin

### <a name="use-drivers-and-sdk"></a>Использование драйверов и пакетов SDK

Заголовки, возвращаемые API Gremlin, сопоставляются с пользовательскими атрибутами состояния, которые сейчас предоставляются через пакеты SDK Gremlin для .NET и Java. Сведения о стоимости запроса доступны в ключе `x-ms-request-charge`.

### <a name="use-the-net-sdk"></a>Использование пакета SDK для .NET

Если вы используете [пакет SDK Gremlin для .NET](https://www.nuget.org/packages/Gremlin.Net/) (его применение описано в [этом кратком руководстве](create-graph-dotnet.md)), атрибуты состояния доступны в свойстве `StatusAttributes` объекта `ResultSet<>`.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>Использование пакета SDK для Java

Если вы используете [пакет SDK Gremlin для Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) (его применение описано в [этом кратком руководстве](create-graph-java.md)), атрибуты состояния можно получить, вызвав метод `statusAttributes()` для объекта `ResultSet`.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>API таблицы

Сейчас для операций с таблицами только один пакет SDK возвращает сведения о расходах в единицах запроса. Это [пакет SDK для .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) (его использование описано в [этом кратком руководстве](create-table-dotnet.md)). Объект `TableResult` предоставляет свойство `RequestCharge`, которое указывается пакетом SDK, при использовании с API таблиц Azure Cosmos DB.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь со следующими статьями, которые содержат сведения об оптимизации потребления единиц запросов:

* [Пропускная способность и единицы запросов в Azure Cosmos DB](request-units.md)
* [Optimize provisioned throughput cost in Azure Cosmos DB](optimize-cost-throughput.md) (Оптимизация стоимости подготовленной пропускной способности в Azure Cosmos DB)
* [Optimize query cost in Azure Cosmos DB](optimize-cost-queries.md) (Оптимизация затрат на отправку запросов в Azure Cosmos DB)
* [Globally scale provisioned throughput](scaling-throughput.md) (Глобальное масштабирование подготовленной пропускной способности)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Provision throughput for an Azure Cosmos DB container](how-to-provision-container-throughput.md) (Подготовка пропускной способности для контейнера Azure Cosmos DB)
