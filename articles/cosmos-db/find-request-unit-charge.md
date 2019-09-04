---
title: Определение расходов в единицах запроса (ЕЗ) в Azure Cosmos DB
description: Узнайте, как определить расходы в единицах запроса (ЕЗ) для любой операции, выполняемой в контейнере Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: c5699bb851bd0a818a987228155c62683e93f51a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240789"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Определение расходов в единицах запроса в Azure Cosmos DB

В этой статье описано несколько способов, позволяющих определить потребление [единиц запроса](request-units.md) (ЕЗ) для любой операции, которая выполняется в контейнере Azure Cosmos DB. Сейчас потребление можно оценить только с помощью портала Azure или сведений из ответа, который Azure Cosmos DB возвращает через пакеты SDK.

## <a name="sql-core-api"></a>API SQL (Core)

Если вы используете API SQL, оценить потребление ЕЗ для операций в контейнере Azure Cosmos можно несколькими способами.

### <a name="use-the-azure-portal"></a>Использование портала Azure

Сейчас на портале Azure можно узнать расходы в ЕЗ только для SQL-запросов.

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте учетную запись Azure Cosmos](create-sql-api-dotnet.md#create-account) и заполните ее данными или выберите существующую учетную запись Azure Cosmos, которая уже содержит данные.

1. Перейдите на панель **обозревателя данных** и выберите контейнер, с которым собираетесь работать.

1. Выберите **Создать SQL-запрос**.

1. Введите допустимый запрос и щелкните **Выполнить запрос**.

1. Выберите **Статистика запросов**, чтобы отобразилось количество ЕЗ, фактически затраченных на выполненный запрос.

![Снимок экрана со сведениями о расходах в ЕЗ на SQL-запрос на портале Azure](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>Использование пакета SDK для .NET
### <a name="net-v2-sdk"></a>Пакет SDK для .Net версии 2

Объекты, возвращаемые из [пакета SDK для .NET версии 2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) предоставляют свойство `RequestCharge`.

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

### <a name="net-v3-sdk"></a>Пакет SDK для .Net версии 3

Объекты, возвращаемые из [пакета SDK для .NET версии 3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) предоставляют свойство `RequestCharge`:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Дополнительные сведения см. в [кратком руководстве Создание веб-приложения .NET с помощью учетной записи API SQL в Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Использование пакета SDK для Java

Объекты, возвращаемые из [пакета SDK для Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) предоставляют метод `getRequestCharge()`.

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

Дополнительные сведения см. в [кратком руководстве Создание веб-приложения Java с использованием учетной записи API SQL для Azure Cosmos DB](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Использование пакета SDK для Node.js

Объекты, возвращаемые из [пакета SDK для Node.js](https://www.npmjs.com/package/@azure/cosmos) предоставляют вложенный объект `headers`, который позволяет сопоставить все заголовки, возвращаемые базовым интерфейсом API для HTTP. Сведения о расходах в ЕЗ доступны в ключе `x-ms-request-charge`.

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

Дополнительные сведения см. в [кратком руководстве Создание приложения Node.js с помощью учетной записи API SQL для Azure Cosmos DB](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Использование пакета SDK для Python

Объект `CosmosClient`, возвращаемый из [пакета SDK для Python](https://pypi.org/project/azure-cosmos/) предоставляет словарь `last_response_headers`, который позволяет сопоставить все заголовки для последней выполненной операции, возвращаемые базовым API HTTP. Сведения о расходах в ЕЗ доступны в ключе `x-ms-request-charge`.

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Дополнительные сведения см. в [кратком руководстве Azure Cosmos DB — создание веб-приложения Python с использованием учетной записи API SQL для Azure Cosmos DB](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>API Azure Cosmos DB для MongoDB

Расходы в ЕЗ предоставляются пользовательской [командой базы данных](https://docs.mongodb.com/manual/reference/command/) с именем `getLastRequestStatistics`. Эта команда возвращает документ, содержащий имя последней выполненной операции, ее длительность и количество затраченных ЕЗ. Если вы используете API Azure Cosmos DB для MongoDB, получить данные о расходах в ЕЗ можно несколькими способами.

### <a name="use-the-azure-portal"></a>Использование портала Azure

Сейчас на портале Azure можно узнать расходы в ЕЗ только для запросов.

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте учетную запись Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) и заполните ее данными или выберите существующую учетную запись, которая уже содержит данные.

1. Перейдите на панель **обозревателя данных** и выберите контейнер, с которым собираетесь работать.

1. Выберите пункт **Создать запрос**.

1. Введите допустимый запрос и щелкните **Выполнить запрос**.

1. Выберите **Статистика запросов**, чтобы отобразилось количество ЕЗ, фактически затраченных на выполненный запрос.

![Снимок экрана со сведениями о расходах в ЕЗ для MongoDB на портале Azure](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Использование драйвера .NET для MongoDB

При использовании [официального драйвера .NET для MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/) можно выполнять команды, вызывая метод `RunCommand` для объекта `IMongoDatabase`. Для работы этого метода необходимо реализовать абстрактный класс `Command<>`.

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

Дополнительные сведения см. в [кратком руководстве Создание веб-приложения .NET с использованием API Azure Cosmos DB для MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Использование драйвера Java для MongoDB


При использовании [официального драйвера Java для MongoDB](https://mongodb.github.io/mongo-java-driver/) можно выполнять команды, вызывая метод `runCommand` для объекта `MongoDatabase`.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Дополнительные сведения см. в [кратком руководстве Создание веб-приложения с помощью API Azure Cosmos DB для MongoDB и пакета SDK для Java](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Использование драйвера Node.js для MongoDB

При использовании [официального драйвера Node.js для MongoDB](https://mongodb.github.io/node-mongodb-native/) можно выполнять команды, вызывая метод `command` для объекта `db`.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Дополнительные сведения см. в [кратком руководстве Перенос существующего веб-приложения MongoDB на Node.js в Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>API Cassandra

Если вы выполняете операции с API Cassandra для Azure Cosmos DB, сведения о расходах в ЕЗ возвращаются во входящих полезных данных в поле с именем `RequestCharge`. Есть несколько способов получения данных о расходах в ЕЗ.

### <a name="use-the-net-sdk"></a>Использование пакета SDK для .NET

При использовании [пакета SDK для .NET](https://www.nuget.org/packages/CassandraCSharpDriver/) вы можете получить входящие полезные данные в свойстве `Info` объекта `RowSet`.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Дополнительные сведения см. в [кратком руководстве Создание приложения Cassandra с помощью пакета SDK для .NET и Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Использование пакета SDK для Java

При использовании [пакета SDK для Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) входные полезные данные можно получить, вызвав метод `getExecutionInfo()` для объекта `ResultSet`.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Дополнительные сведения см. в [кратком руководстве Создание приложения Cassandra с помощью пакета SDK для Java и Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>API Gremlin

При использовании API Gremlin оценить потребление в ЕЗ для операций в контейнере Azure Cosmos можно несколькими способами. 

### <a name="use-drivers-and-sdk"></a>Использование драйверов и пакетов SDK

Заголовки, возвращаемые API Gremlin, сопоставляются с пользовательскими атрибутами состояния, которые сейчас предоставляются через пакеты SDK Gremlin для .NET и Java. Сведения о стоимости запроса доступны в ключе `x-ms-request-charge`.

### <a name="use-the-net-sdk"></a>Использование пакета SDK для .NET

При использовании [пакета SDK Gremlin для .NET](https://www.nuget.org/packages/Gremlin.Net/) атрибуты состояния доступны в свойстве `StatusAttributes` объекта `ResultSet<>`.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Дополнительные сведения см. в [кратком руководстве Создание приложения .NET Framework или Core с помощью учетной записи API Gremlin для Azure Cosmos DB](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Использование пакета SDK для Java

При использовании [пакета SDK Gremlin для Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) атрибуты состояния можно получить, вызвав метод `statusAttributes()` для объекта `ResultSet`.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Дополнительные сведения см. в [кратком руководстве Создание графовой базы данных в Azure Cosmos DB с помощью пакета SDK для Java](create-graph-java.md).

## <a name="table-api"></a>API таблиц

Единственный пакет SDK, который сейчас возвращает данные о расходах в ЕЗ для операций с таблицами, — это [пакет SDK для .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Объект `TableResult` предоставляет свойство `RequestCharge`, которое указывается пакетом SDK, при использовании с API таблиц Azure Cosmos DB.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Дополнительные сведения см. в [кратком руководстве Создание приложения API таблиц с помощью пакета SDK .NET и Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Следующие шаги

Сведения об оптимизации потребления ЕЗ см. в следующих статьях:

* [Пропускная способность и единицы запросов в Azure Cosmos DB](request-units.md)
* [Optimize provisioned throughput cost in Azure Cosmos DB](optimize-cost-throughput.md) (Оптимизация стоимости подготовленной пропускной способности в Azure Cosmos DB)
* [Optimize query cost in Azure Cosmos DB](optimize-cost-queries.md) (Оптимизация затрат на отправку запросов в Azure Cosmos DB)
* [Globally scale provisioned throughput](scaling-throughput.md) (Глобальное масштабирование подготовленной пропускной способности)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Подготовка пропускной способности для контейнера](how-to-provision-container-throughput.md)
* [Monitor and debug with metrics in Azure Cosmos DB](use-metrics.md) (Мониторинг и отладка с помощью метрик в Azure Cosmos DB)
