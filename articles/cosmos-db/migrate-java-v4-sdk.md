---
title: Перенос приложения для использования пакета SDK для Azure Cosmos DB Java версии 4 (com. Azure. Cosmos)
description: Узнайте, как обновить существующее приложение Java от использования старых пакетов SDK для Java Azure Cosmos DB до нового пакета SDK для Java 4,0 (com. Azure. Cosmos Package) для Core (SQL) API.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.reviewer: sngun
ms.openlocfilehash: 929fa936cdb864fd9b84f8feba55ef01ae6fed9c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984711"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Перенос приложения для использования пакета SDK для Azure Cosmos DB Java версии 4

> [!IMPORTANT]  
> Дополнительные сведения об этом пакете SDK см. в заметках о выпуске Azure Cosmos DB Java SDK V4, [репозитории Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB [Советы по повышению производительности](performance-tips-java-sdk-v4-sql.md)пакета java SDK v4 и Azure Cosmos DB [руководство по устранению неполадок](troubleshoot-java-sdk-v4-sql.md)пакета Java SDK v4.
>

В этой статье объясняется, как обновить существующее приложение Java, которое использует более старый пакет SDK для Azure Cosmos DB Java, до нового Azure Cosmos DB пакета SDK для Java 4,0 для Core (SQL) API. Azure Cosmos DB `com.azure.cosmos` пакета SDK версии 4 для Java соответствует пакету. При переносе приложения из любого из следующих Azure Cosmos DB пакетов SDK для Java можно использовать инструкции из этого документа: 

* Синхронизация пакета SDK для Java 2. x. x
* Async Java SDK 2. x. x
* Пакет SDK для Java 3. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB пакета SDK для Java и сопоставления пакетов

В следующей таблице перечислены различные Azure Cosmos DB пакеты SDK для Java, имя пакета и сведения о выпуске:

| Пакет SDK для Java| Дата выпуска | Объединенные API   | JAR-файл Maven  | Имя пакета Java  |Справочник по API   | Заметки о выпуске  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2. x. x  | Июнь 2018 г.    | Async (Рксжава)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Заметки о выпуске](sql-api-sdk-async-java.md) |
| Синхронизация 2. x. x     | Сентябрь 2018    | Синхронизация   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Заметки о выпуске](sql-api-sdk-java.md)  |
| 3. x. x    | Июль 2019 г.    | Async (реактора)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4,0   | Апрель 2020 г.   | Async (реактора)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | -  |

## <a name="sdk-level-implementation-changes"></a>Изменения реализации уровня пакета SDK

Ниже приведены основные различия в реализации разных пакетов SDK.

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>Рксжава заменяется на реактора в Azure Cosmos DB пакете SDK для Java версии 3. x. x и 4,0

Если вы не знакомы с асинхронным программированием или реактивным программированием, ознакомьтесь с [руководством по шаблону реактора](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) , в котором представлены общие сведения о асинхронном программировании и проекте реактора. Это пошаговое руководством может оказаться полезным, если вы уже использовали Azure Cosmos DB Sync SDK Java с пакетом API 2. x. x или Azure Cosmos DB Java SDK 3. x. x Sync.

Если вы используете Azure Cosmos DB Async SDK для Java 2. x. x и планируете переход на пакет SDK 4,0, ознакомьтесь с [руководством по реактора VS рксжава](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) , где приведены рекомендации по преобразованию кода рксжава для использования реактора.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB пакет SDK для Java v4 имеет режим прямого подключения как для асинхронных интерфейсов, так и для API синхронизации.

Если вы используете Azure Cosmos DB Sync SDK 2. x. x для Java, обратите внимание, что режим прямого подключения на основе протокола TCP (в отличие от HTTP) реализован в пакете Azure Cosmos DB пакета SDK для Java 4,0 для асинхронных и синхронных API-интерфейсов.

## <a name="api-level-changes"></a>Изменения на уровне API

Ниже приведены изменения уровня API в Azure Cosmos DB пакете SDK для Java 4. x. x по сравнению с предыдущими пакетами SDK (Java SDK 3. x. x, async Java SDK 2. x. x и Sync SDK Java 2. x. x):

![Соглашения об именовании пакетов SDK для Java Azure Cosmos DB](./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png)

* Azure Cosmos DB пакета SDK для Java 3. x. x и 4,0 см. в ресурсах клиента как `Cosmos<resourceName>`. Например `CosmosClient`, `CosmosDatabase`, `CosmosContainer`. В то время как в версии 2. x. x Azure Cosmos DB пакеты SDK для Java не имеют единообразной схемы именования.

* Azure Cosmos DB пакета SDK для Java 3. x. x и 4,0 предлагают как синхронные, так и асинхронные API.

  * **Пакет SDK для Java 4,0** . все классы ПРИНАДЛЕЖАТ к API синхронизации, если только имя класса не добавлено с `Async` помощью `Cosmos`After.

  * **Пакет SDK для Java 3. x. x**. все классы принадлежат асинхронному API, если только имя класса не добавлено с `Async` помощью `Cosmos`After.

  * **Async Java SDK 2. x. x**. имена классов похожи на СИНХРОНИЗАЦИЮ пакета SDK для Java 2. x. x, однако имя начинается с *Async*.

### <a name="hierarchical-api-structure"></a>Иерархическая структура API

Azure Cosmos DB пакета SDK для Java 4,0 и 3. x. x представляют иерархическую структуру API, которая упорядочивает клиенты, базы данных и контейнеры во вложенном виде, как показано в следующем фрагменте кода пакета SDK для 4,0:

```java
CosmosContainer = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");
```

В версии 2. x. x Azure Cosmos DB пакета SDK для Java все операции с ресурсами и документами выполняются через экземпляр клиента.

### <a name="representing-documents"></a>Представление документов

В Azure Cosmos DB пакет SDK для Java 4,0, пользовательские POJO `JsonNodes` и — это два варианта чтения и записи документов из Azure Cosmos DB.

В Azure Cosmos DB пакете SDK для Java 3. x. x `CosmosItemProperties` объект предоставляется открытым API и обрабатывается как представление документа. Этот класс больше не предоставляется в общедоступной версии 4,0.

### <a name="imports"></a>Импорт

* Azure Cosmos DB пакеты SDK для Java пакета 4,0 начинаются с`com.azure.cosmos`
  * Azure Cosmos DB пакеты SDK для Java 3. x. x начинаются с`com.azure.data.cosmos`

* Azure Cosmos DB пакета SDK для Java 4,0 размещает несколько классов во `com.azure.cosmos.models`вложенном пакете. Ниже перечислены некоторые из этих пакетов.

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Аналоги асинхронного API для всех перечисленных выше пакетов
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode`... 123.

### <a name="accessors"></a>Методы доступа

Azure Cosmos DB пакета SDK для Java `get` 4,0 `set` предоставляет методы и для доступа к членам экземпляра. Например, у `CosmosContainer` экземпляра есть `container.getId()` методы и `container.setId()` .

Это отличается от Azure Cosmos DB пакета SDK для Java 3. x. x, предоставляющего интерфейс Fluent. Например, `CosmosSyncContainer` экземпляр, `container.id()` который перегружается для получения или задания `id` значения.

## <a name="code-snippet-comparisons"></a>Сравнения фрагментов кода

### <a name="create-resources"></a>Создание ресурсов

В следующем фрагменте кода показаны различия между созданием ресурсов между асинхронными API 4,0 и 3. x. x.

# <a name="java-sdk-40-async-api"></a>[Асинхронный API пакета SDK для Java 4,0](#tab/java-v4-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.getDefaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));
// Use Direct Mode for best performance
defaultPolicy.setConnectionMode(ConnectionMode.DIRECT);

// Create Async client.
// Building an async client is still a sync operation.
client = new CosmosClientBuilder()
        .setEndpoint("your.hostname")
        .setKey("yourmasterkey")
        .setConnectionPolicy(ConnectionPolicy.getDefaultPolicy())
        .setConsistencyLevel(ConsistencyLevel.EVENTUAL)
        .buildAsyncClient();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databas'Response -> {
        database = databaseResponse.getDatabase();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContaine'Properties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.getContainer();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[Асинхронный API пакета SDK для Java 3. x. x](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPo"ic"();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database"createContainerIf"otExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```
---

### <a name="item-operations"></a>Операции с элементами

В следующем фрагменте кода показаны различия между выполнением операций элементов между асинхронными API 4,0 и 3. x. x.

# <a name="java-sdk-40-async-api"></a>[Асинхронный API пакета SDK для Java 4,0](#tab/java-v4-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-3xx-async-api"></a>[Асинхронный API пакета SDK для Java 3. x. x](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```
---

### <a name="indexing"></a>Индексация

В следующем фрагменте кода показаны различия между созданием индекса между асинхронными API 4,0 и 3. x. x.

# <a name="java-sdk-40-async-api"></a>[Асинхронный API пакета SDK для Java 4,0](#tab/java-v4-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); 

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

containerProperties.setIndexingPolicy(indexingPolicy);

CosmosAsyncContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                                    .block()
                                                    .getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[Асинхронный API пакета SDK для Java 3. x. x](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```
---

### <a name="stored-procedures"></a>Хранимые процедуры

В следующем фрагменте кода показаны различия между созданием хранимых процедур между асинхронными API 4,0 и 3. x. x.

# <a name="java-sdk-40-async-api"></a>[Асинхронный API пакета SDK для Java 4,0](#tab/java-v4-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.getResponseAsString(),
                    executeResponse.getStatusCode(),
                    executeResponse.getRequestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-3xx-async-api"></a>[Асинхронный API пакета SDK для Java 3. x. x](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```
---

### <a name="change-feed"></a>Канал изменений

В следующем фрагменте кода показаны различия между выполнением операций веб-канала изменений между асинхронными API 4,0 и 3. x. x.

# <a name="java-sdk-40-async-api"></a>[Асинхронный API пакета SDK для Java 4,0](#tab/java-v4-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.changeFeedProcessorBuilder()
    .setHostName(hostName)
    .setFeedContainer(feedContainer)
    .setLeaseContainer(leaseContainer)
    .setHandleChanges((List<JsonNode> docs) -> {
        logger.info("--->setHandleChanges() START");

        for (JsonNode document : docs) {
            try {
                //Change Feed hands the document to you in the form of a JsonNode
                //As a developer you have two options for handling the JsonNode document provided to you by Change Feed
                //One option is to operate on the document in the form of a JsonNode, as shown below. This is great
                //especially if you do not have a single uniform data model for all documents.
                logger.info("---->DOCUMENT RECEIVED: " + OBJECT_MAPPER.writerWithDefaultPrettyPrinter()
                        .writeValueAsString(document));

                //You can also transform the JsonNode to a POJO having the same structure as the JsonNode,
                //as shown below. Then you can operate on the POJO.
                CustomPOJO pojo_doc = OBJECT_MAPPER.treeToValue(document, CustomPOJO.class);
                logger.info("----=>id: " + pojo_doc.getId());

            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
        }
        logger.info("--->handleChanges() END");

    })
    .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[Асинхронный API пакета SDK для Java 3. x. x](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```
---

### <a name="container-level-time-to-livettl"></a>Срок жизни на уровне контейнера (TTL)

В следующем фрагменте кода показаны различия в создании срока жизни данных в контейнере с помощью асинхронных API 4,0 и 3. x. x:

# <a name="java-sdk-40-async-api"></a>[Асинхронный API пакета SDK для Java 4,0](#tab/java-v4-async)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[Асинхронный API пакета SDK для Java 3. x. x](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="item-level-time-to-livettl"></a>Срок жизни (TTL) на уровне элемента

В следующем фрагменте кода показаны различия в создании срока жизни для элемента с помощью асинхронных API 4,0 и 3. x. x:

# <a name="java-sdk-40-async-api"></a>[Асинхронный API пакета SDK для Java 4,0](#tab/java-v4-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-3xx-async-api"></a>[Асинхронный API пакета SDK для Java 3. x. x](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```
---

## <a name="next-steps"></a>Дальнейшие действия

* [Создание приложения Java](create-sql-api-java.md) для управления Azure Cosmos DB данных API SQL с помощью пакета SDK v4
* Дополнительные сведения о пакетах [SDK для Java на основе реактора](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)
* Дополнительные сведения о преобразовании асинхронного кода Рксжава в реактора асинхронный код с помощью [инструкции по реактора VS рксжава](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)