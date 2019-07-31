---
title: Как вызывать хранимые процедуры, триггеры и определяемые пользователем функции с помощью пакетов SDK для Azure Cosmos DB
description: Узнайте, как зарегистрировать и вызывать хранимые процедуры, триггеры и определяемые пользователем функции с помощью пакетов SDK для Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/21/2019
ms.author: mjbrown
ms.openlocfilehash: e1215441b45e1fdba8d74c9149415f0da5c4c44a
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68360382"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Как зарегистрировать и использовать хранимые процедуры, триггеры и определяемые пользователем функции в Azure Cosmos DB

API SQL в Azure Cosmos DB поддерживает регистрацию и вызов хранимых процедур, триггеров и определяемых пользователем функций (UDF), написанных на языке JavaScript. Вы можете использовать SQL API [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md) или пакеты SDK для [Python](sql-api-sdk-python.md), чтобы регистрировать и вызывать хранимые процедуры. После определения одной или более хранимых процедур, триггеров и определяемых пользователем функций вы сможете загружать и просматривать их на [портале Azure](https://portal.azure.com/) с помощью обозревателя данных.

## <a id="stored-procedures"></a>Выполнение хранимых процедур

Хранимые процедуры написаны с использованием JavaScript. Они могут создавать, обновлять, читать, запрашивать и удалять элементы в контейнере Azure Cosmos. Дополнительные сведения о том, как написать хранимую процедуру в Azure Cosmos DB см. в статье [Как писать хранимые процедуры, триггеры и определяемые пользователем функции в Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures).

В следующих примерах показано, как регистрировать и вызывать хранимую процедуру с помощью пакетов SDK для Azure Cosmos DB. Используйте раздел [Создание документа](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) как источник для этой хранимой процедуры, которая сохраняется в виде `spCreateToDoItem.js`.

> [!NOTE]
> Для секционированных контейнеров при выполнении хранимой процедуры в параметрах запроса необходимо указать значение ключа секции. Хранимые процедуры всегда ограничиваются ключом секции. Элементы с другим значением ключа секции не будут видны хранимой процедуре. Это также применяется к триггерам.

### <a name="stored-procedures---net-sdk"></a>Хранимые процедуры — пакет SDK для .NET

Ниже показано, как правильно зарегистрировать хранимую процедуру с помощью пакета SDK для .NET:

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

В коде ниже показано, как правильно вызывать хранимую процедуру с помощью пакета SDK для .NET:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
var id = result.Response;
```

### <a name="stored-procedures---java-sdk"></a>Хранимые процедуры — пакет SDK для Java

Ниже показано, как правильно зарегистрировать хранимую процедуру с помощью пакета SDK для Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

В коде ниже показано, как правильно вызывать хранимую процедуру с помощью пакета SDK для Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Хранимые процедуры — пакет SDK для JavaScript

Ниже показано, как правильно зарегистрировать хранимую процедуру с помощью пакета SDK для JavaScript

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

В коде ниже показано, как правильно вызывать хранимую процедуру с помощью пакета SDK для JavaScript:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Хранимые процедуры — пакет SDK для Python

Ниже показано, как правильно зарегистрировать хранимую процедуру с помощью пакета SDK для Python

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

В коде ниже показано, как правильно вызывать хранимую процедуру с помощью пакета SDK для Python

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>Как выполнять предварительные триггеры

В следующих примерах показано, как регистрировать и вызывать предварительный триггер с помощью пакетов SDK для Azure Cosmos DB. Используйте раздел [Пример предварительного триггера](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) как источник для этого предварительного триггера, который сохраняется как `trgPreValidateToDoItemTimestamp.js`.

При выполнении предварительные триггеры передаются в объекте RequestOptions. Для этого нужно указать `PreTriggerInclude` и передать имя триггера в объект List.

> [!NOTE]
> Несмотря на то что имя триггера передается в виде списка, по-прежнему можно выполнить только один триггер на каждую операцию.

### <a name="pre-triggers---net-sdk"></a>Предварительные триггеры — пакет SDK для .NET

Ниже показано, как зарегистрировать предварительный триггер с помощью пакета SDK для .NET:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Ниже показано, как вызывать предварительный триггер с помощью пакета SDK для .NET:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>Предварительные триггеры — пакет SDK для Java

Ниже показано, как зарегистрировать предварительный триггер с помощью пакета SDK для Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Ниже показано, как вызывать предварительный триггер с помощью пакета SDK для Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Предварительные триггеры — пакет SDK для JavaScript

Ниже показано, как зарегистрировать предварительный триггер с помощью пакета SDK для JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

Ниже показано, как вызвать предварительный триггер с помощью пакета SDK для JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Предварительные триггеры — пакет SDK для Python

Ниже показано, как зарегистрировать предварительный триггер с помощью пакета SDK для Python:

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Ниже показано, как вызвать предварительный триггер с помощью пакета SDK для Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Как выполнять триггеры после операции

В следующих примерах показано, как регистрировать триггер после операции с помощью пакетов SDK для Azure Cosmos DB. Используйте раздел [Пример триггера после операции](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) как источник для этого триггера после операции, который сохраняется как `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk"></a>Триггеры после операции — пакет SDK для .NET

Ниже показано, как зарегистрировать триггер после операции с помощью пакета SDK для .NET:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Ниже показано, как вызывать триггер после операции с помощью пакета SDK для .NET:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>Триггеры после операции — пакет SDK для Java

Ниже показано, как зарегистрировать триггер после операции с помощью пакета SDK для Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Ниже показано, как вызывать триггер после операции с помощью пакета SDK для Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Триггеры после операции — пакет SDK для JavaScript

Ниже показано, как зарегистрировать триггер после операции с помощью пакета SDK для JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

Ниже показано, как вызывать триггер после операции с помощью пакета SDK для JavaScript:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Триггеры после операции — пакет SDK для Python

Ниже показано, как зарегистрировать триггер после операции с помощью пакета SDK для Python:

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPostUpdateMetadata',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Ниже показано, как вызывать триггер после операции с помощью пакета SDK для Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Работа с определяемыми пользователем функциями

В следующих примерах показано, как зарегистрировать определяемую пользователем функцию с помощью пакетов SDK для Azure Cosmos DB. Используйте раздел [Пример определяемой пользователем функции](how-to-write-stored-procedures-triggers-udfs.md#udfs) как источник для этого триггера после операции, который сохраняется как `udfTax.js`.

### <a name="user-defined-functions---net-sdk"></a>Определяемые пользователем функции — пакет SDK для .NET

Ниже показано, как зарегистрировать определяемую пользователем функцию с помощью пакета SDK для .NET:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

Ниже показано, как вызывать определяемую пользователем функцию с помощью пакета SDK для .NET:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Определяемые пользователем функции — пакет SDK для Java

Ниже показано, как зарегистрировать определяемую пользователем функцию с помощью пакета SDK для Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

Ниже показано, как вызывать определяемую пользователем функцию с помощью пакета SDK для Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Определяемые пользователем функции — пакет SDK для JavaScript

Ниже показано, как зарегистрировать определяемую пользователем функцию с помощью пакета SDK для JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

Ниже показано, как вызывать определяемую пользователем функцию с помощью пакета SDK для JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Определяемые пользователем функции — пакет SDK для Python

Ниже показано, как зарегистрировать определяемую пользователем функцию с помощью пакета SDK для Python:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

Ниже показано, как вызывать определяемую пользователем функцию с помощью пакета SDK для Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о том, как записать или использовать хранимые процедуры, триггеры и определяемые пользователем функции в Azure Cosmos DB, см. в статьях ниже:

- [Working with Azure Cosmos DB stored procedures, triggers, and user-defined functions](stored-procedures-triggers-udfs.md) (Работа с хранимыми процедурами, триггерами и определяемыми пользователем функциями в Azure Cosmos DB)
- [Working with JavaScript language-integrated query API with Azure Cosmos DB](javascript-query-api.md) (Работа с API запросов на основе языка JavaScript в Azure Cosmos DB)
- [Как писать хранимые процедуры, триггеры и определяемые пользователем функции в Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [How to write stored procedures and triggers in Azure Cosmos DB by using the JavaScript query API](how-to-write-javascript-query-api.md) (Как записывать хранимые процедуры и триггеры с помощью API запросов JavaScript в Azure Cosmos DB)
