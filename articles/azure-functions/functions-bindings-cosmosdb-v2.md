---
title: Привязки Azure Cosmos DB для службы "Функции" версии 2.х
description: Узнайте, как использовать триггеры и привязки Azure Cosmos DB в службе "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 0ad569977194441b026c2c987ecad544ce40cfa1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227360"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Привязки Azure Cosmos DB для службы "Функции Azure" версии 2.х

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Версия 1](functions-bindings-cosmosdb.md)
> * [Версия 2](functions-bindings-cosmosdb-v2.md)

В этой статье описано, как использовать привязки [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) в службе "Функции Azure" версии 2.x. Служба "Функции Azure" поддерживает привязки триггера, а также входные и выходные привязки для Azure Cosmos DB.

> [!NOTE]
> Эта статья предназначена для службы ["Функции Azure" версии 2.x](functions-versions.md).  Для получения дополнительных сведений об использовании Функций версии 1.x см. раздел [Функции 1.х](functions-bindings-cosmosdb.md).
>
> Эта привязка называлась DocumentDB. В Функциях версии 2.x триггер, привязки и пакет вместе называются Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>Поддерживаемые API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

Привязки Azure Cosmos DB для Функций версии 2.x доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) версии 3.х. Исходный код для привязок находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Триггер

Триггер Azure Cosmos DB использует [канал изменений Azure Cosmos DB](../cosmos-db/change-feed.md) для ожидания вставок и обновлений в разных разделах. На канале изменений публикуются вставки и обновления, а не удаления.

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Сценарий C# (CSX)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

Пропустить примеры триггеров

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показана [функция C#](functions-dotnet-class-library.md), вызываемая при вставке или обновлении в указанной базе данных и коллекции.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

Пропустить примеры триггеров

### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показаны привязка триггера Cosmos DB в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. При изменении записей в Cosmos DB функция записывает сообщения в журнале.

Данные привязки в файле *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Ниже приведен код скрипта C#.

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

Пропустить примеры триггеров

### <a name="trigger---javascript-example"></a>Пример JavaScript в триггере

В следующем примере показаны привязка триггера Cosmos DB в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. При изменении записей в Cosmos DB функция записывает сообщения в журнале.

Данные привязки в файле *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Ниже показан код JavaScript.

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

### <a name="trigger---java-example"></a>Пример Java в триггере

В следующем примере показаны привязка триггера Cosmos DB в файле *function.json* и [функция Java](functions-reference-java.md), которая использует эту привязку. Эта функция используется при вставке или обновлении в указанной базе данных и коллекции.

```json
{
    "type": "cosmosDBTrigger",
    "name": "items",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "AzureCosmosDBConnection",
    "databaseName": "ToDoList",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": false
}
```

Ниже приведен код Java.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@CosmosDBTrigger` для параметров, значения которых будут поступать из Cosmos DB.  This annotation can be used with native Java types, POJOs, or nullable values using Optional\<T>.


Пропустить примеры триггеров

### <a name="trigger---python-example"></a>Пример Python: триггер

В следующем примере показаны привязка триггера Cosmos DB в файле *function.json* и [функция Python](functions-reference-python.md), которая использует эту привязку. При изменении записей в Cosmos DB функция записывает сообщения в журнале.

Данные привязки в файле *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Ниже приведен код Python.

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

## <a name="trigger---c-attributes"></a>C# атрибуты триггера

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs).

Конструктор атрибута принимает имя базы данных и имя коллекции. Дополнительные сведения о параметрах и других свойствах, которые можно настроить, см. в разделе [Конфигурация триггера](#trigger---configuration). Ниже приведен пример атрибута `CosmosDBTrigger` в сигнатуре метода:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Полный пример см. в разделе [Пример C# в триггере](#trigger---c-example).


## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `CosmosDBTrigger`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** || Нужно задать значение `cosmosDBTrigger`. |
|**direction** || Нужно задать значение `in`. Этот параметр задается автоматически при создании триггера на портале Azure. |
|**name** || Имя переменной, используемое в коде функции, представляющей список документов с изменениями. |
|**connectionStringSetting**|**ConnectionStringSetting** | Имя параметра приложения, содержащего строку подключения, используемую для подключения к отслеживаемой учетной записи Azure Cosmos DB. |
|**databaseName**|**DatabaseName**  | Имя базы данных Azure Cosmos DB с отслеживаемой коллекцией. |
|**collectionName** |**CollectionName** | Имя отслеживаемой коллекции. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Необязательно.) Имя параметра приложения, содержащее строку подключения для службы, содержащей коллекцию аренды. Если значение не задано, используется значение `connectionStringSetting`. Этот параметр задается автоматически при создании привязки на портале. Строка подключения для коллекции аренд должна иметь разрешения на запись.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Необязательно.) Имя базы данных, в которой содержится коллекция, используемая для хранения аренд. Если значение не задано, используется значение параметра `databaseName`. Этот параметр задается автоматически при создании привязки на портале. |
|**leaseCollectionName** | **LeaseCollectionName** | (Необязательно.) Имя коллекции, используемой для хранения аренд. Если значение не задано, используется значение `leases`. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Необязательно.) Если задано значение `true`, коллекция аренд создается автоматически, если она не создана. По умолчанию используется значение `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Необязательно.) Определяет количество единиц запросов для назначения при создании коллекции аренд. Этот параметр используется, только если для `createLeaseCollectionIfNotExists` задано значение `true`. Этот параметр задается автоматически при создании привязки с помощью портала.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Дополнительно) Если параметр задан, то он добавляет префикс к созданной аренде в коллекции аренды для этой функции, позволяя двум разным функциям Azure совместно эффективно использовать коллекцию аренд с помощью различных префиксов.
|**feedPollDelay**| **FeedPollDelay**| (Дополнительно) Если параметр задан, то он определяет задержку между опросами секции на наличие новых изменений в веб-канале, после того как все текущие изменения будут утеряны (в миллисекундах). По умолчанию это 5000 (5 секунд).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Дополнительно) Если параметр задан, то он определяет интервал для запуска задачи вычисления при условии равномерности распределения секций между известными экземплярами узла (в миллисекундах). По умолчанию это 13000 (13 секунд).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Дополнительно) Если параметр задан, то он определяет интервал, за который берется аренда, представляющая секцию (в миллисекундах). Если аренда не будет обновлена в течение этого интервала, это приведет к ее истечению и владение секцией перейдет к другому экземпляру. По умолчанию это 60000 (60 секунд).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Дополнительно) Если параметр задан, то он определяет интервал продления для всех аренд в разделах, которые на данный момент занятые экземплярами (в миллисекундах). По умолчанию это 17000 (17 секунд).
|**checkpointFrequency**| **CheckpointFrequency**| (Дополнительно) Если параметр задан, то он определяет интервал между контрольными точками аренды (в миллисекундах). После каждого вызова функции всегда используется значение по умолчанию.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Optional) When set, this property sets the maximum amount of items received per Function call. If operations in the monitored collection are performed through stored procedures, [transaction scope](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) is preserved when reading items from the Change Feed. As a result of this, it's possible for the amount of items received to be higher than the specified value so that the items changed by the same transaction are returned as part of one atomic batch.
|**startFromBeginning**| **StartFromBeginning**| (Необязательно.) Если параметр задан, значит, триггер может начать чтение изменений с начала журнала коллекции, а не с текущего момента времени. Это возможно только при первом запуске триггера, так как при последующих запусках контрольные точки уже будут сохранены. Если установить значение `true` для этого параметра при уже созданных арендах, этот параметр не будет иметь никакого эффекта.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Использование триггера

Триггеру требуется вторая коллекция, которая используется для хранения _аренды_ в разделах. Отслеживаемая коллекция и та, в которой содержатся аренды, должны быть доступны для успешной работы триггера.

>[!IMPORTANT]
> Если несколько функций настроены на использование триггера Cosmos DB для одной и той же коллекции, то каждая из функций должна использовать выделенную коллекцию аренды или указывать на другой префикс `LeaseCollectionPrefix` для каждой функции. В противном случае активируется только одна из функций. Сведения о префиксе см. в разделе с [конфигурацией](#trigger---configuration).

Триггер не указывает, был ли документ обновлен или вставлен. Вместо этого он представляет сам документ. Если операции обновления и вставки необходимо обрабатывать по-разному, это можно сделать, внедрив поля меток времени для операций обновления и вставки.

## <a name="input"></a>Входные данные

Входная привязка Azure Cosmos DB извлекает один или несколько документов из Azure Cosmos DB и передает их входному параметру функции через API SQL. Идентификатор документа или параметры запроса можно определить по триггеру, который вызывает функцию.

> [!NOTE]
> If the collection is [partitioned](../cosmos-db/partition-data.md#logical-partitions), lookup operations need to also specify the partition key value.
>

## <a name="input---examples"></a>Примеры входных данных

См. примеры (для разных языков), в которых для считывания одного документа указывается значение идентификатора:

* [C#](#input---c-examples)
* [Сценарий C# (CSX)](#input---c-script-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-examples)
* [Python](#input---python-examples)

[Пропустить примеры входных данных](#input---attributes)

### <a name="input---c-examples"></a>Примеры входных данных C#

В этом разделе содержатся следующие примеры:

* [Триггер очереди, поисковый идентификатор из JSON](#queue-trigger-look-up-id-from-json-c)
* [Триггер HTTP, поисковый идентификатор из строки запроса](#http-trigger-look-up-id-from-query-string-c)
* [Триггер HTTP, поисковый идентификатор из данных маршрута](#http-trigger-look-up-id-from-route-data-c)
* [Триггер HTTP, поисковый идентификатор из данных маршрута, используется SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Триггер HTTP, получение нескольких документов, используется SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Триггер HTTP, получение нескольких документов, используется DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

В примерах используется `ToDoItem` простого типа:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Триггер очереди, поисковый идентификатор из JSON (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает один документ. Функция активируется сообщением из очереди, который содержит объект JSON. The queue trigger parses the JSON into an object named `ToDoItemLookup`, which contains the ID and partition key value to look up. That ID and partition key value are used to retrieve a `ToDoItem` document from the specified database and collection.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Триггер HTTP, поисковый идентификатор из строки запроса (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает один документ. The function is triggered by an HTTP request that uses a query string to specify the ID and partition key value to look up. That ID and partition key value are used to retrieve a `ToDoItem` document from the specified database and collection.

>[!NOTE]
>Параметры строки запроса HTTP задаются с учетом регистра.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Триггер HTTP, поисковый идентификатор из данных маршрута (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает один документ. The function is triggered by an HTTP request that uses route data to specify the ID and partition key value to look up. That ID and partition key value are used to retrieve a `ToDoItem` document from the specified database and collection.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Триггер HTTP, поисковый идентификатор из данных маршрута, используется SqlQuery (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает один документ. Функция инициируется HTTP-запросом, в котором с помощью данных маршрута указывается идентификатор для поиска. Этот идентификатор используется для получения `ToDoItem` документа из указанной базы данных и коллекции.

В примере показано, как использовать выражение привязки в параметре `SqlQuery`. Вы можете передать данные маршрута в параметр `SqlQuery`, как показано здесь, но сейчас [невозможно передать значения строки запроса](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> If you need to query by just the ID, it is recommended to use a look up, like the [previous examples](#http-trigger-look-up-id-from-query-string-c), as it will consume less [request units](../cosmos-db/request-units.md). Point read operations (GET) are [more efficient](../cosmos-db/optimize-cost-queries.md) than queries by ID.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>Триггер HTTP, получение нескольких документов, используется SqlQuery (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает список документов. Функция активируется с помощью HTTP-запроса. Запрос указывается в свойстве атрибута `SqlQuery`.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>Триггер HTTP, получение нескольких документов, используется DocumentClient (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает список документов. Функция активируется с помощью HTTP-запроса. Код использует экземпляр `DocumentClient`, предоставленный привязкой Azure Cosmos DB для считывания списка документов. Экземпляр `DocumentClient` может также использоваться для операций записи.

> [!NOTE]
> You can also use the [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) interface to make testing easier.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

### <a name="input---c-script-examples"></a>Примеры входных данных скрипта C#

В этом разделе содержатся следующие примеры:

* [Триггер очереди, поисковый идентификатор из строки](#queue-trigger-look-up-id-from-string-c-script)
* [Триггер очереди, получение нескольких документов, используется SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Триггер HTTP, поисковый идентификатор из строки запроса](#http-trigger-look-up-id-from-query-string-c-script)
* [Триггер HTTP, поисковый идентификатор из данных маршрута](#http-trigger-look-up-id-from-route-data-c-script)
* [Триггер HTTP, получение нескольких документов, используется SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Триггер HTTP, получение нескольких документов, используется DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

В примерах триггера HTTP используется `ToDoItem` простого типа:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Триггер очереди, поисковый идентификатор из строки (скрипт C#)

В следующем примере показаны входная привязка Cosmos DB в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция считывает один документ и обновляет текстовое значение в документе.

Данные привязки в файле *function.json*:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Триггер очереди, получение нескольких документов, используется SqlQuery (скрипт C#)

В следующем примере показана входная привязка Azure Cosmos DB в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция извлекает несколько документов, указанных SQL-запросом, используя триггер очереди для настройки параметров запроса.

Триггер очереди предоставляет параметр `departmentId`. Сообщение из очереди `{ "departmentId" : "Finance" }` возвратит все записи для финансового отдела.

Данные привязки в файле *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>Триггер HTTP, поисковый идентификатор из строки запроса (скрипт C#)

В следующем примере показана [функция скрипта C#](functions-reference-csharp.md), которая получает один документ. The function is triggered by an HTTP request that uses a query string to specify the ID and partition key value to look up. That ID and partition key value are used to retrieve a `ToDoItem` document from the specified database and collection.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Ниже приведен код скрипта C#.

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>Триггер HTTP, поисковый идентификатор из данных маршрута (скрипт C#)

В следующем примере показана [функция скрипта C#](functions-reference-csharp.md), которая получает один документ. The function is triggered by an HTTP request that uses route data to specify the ID and partition key value to look up. That ID and partition key value are used to retrieve a `ToDoItem` document from the specified database and collection.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Ниже приведен код скрипта C#.

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Триггер HTTP, получение нескольких документов, используется SqlQuery (скрипт C#)

В следующем примере показана [функция скрипта C#](functions-reference-csharp.md), которая получает список документов. Функция активируется с помощью HTTP-запроса. Запрос указывается в свойстве атрибута `SqlQuery`.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Ниже приведен код скрипта C#.

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>Триггер HTTP, получение нескольких документов, используется DocumentClient (скрипт C#)

В следующем примере показана [функция скрипта C#](functions-reference-csharp.md), которая получает список документов. Функция активируется с помощью HTTP-запроса. Код использует экземпляр `DocumentClient`, предоставленный привязкой Azure Cosmos DB для считывания списка документов. Экземпляр `DocumentClient` может также использоваться для операций записи.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Ниже приведен код скрипта C#.

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Пропустить примеры входных данных](#input---attributes)

### <a name="input---javascript-examples"></a>Примеры входных данных JavaScript

В этом разделе содержатся следующие примеры, в которых один документ считывается при указании значения идентификатора из разных источников:

* [Триггер очереди, поисковый идентификатор из JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Триггер HTTP, поисковый идентификатор из строки запроса](#http-trigger-look-up-id-from-query-string-javascript)
* [Триггер HTTP, поисковый идентификатор из данных маршрута](#http-trigger-look-up-id-from-route-data-javascript)
* [Триггер очереди, получение нескольких документов, используется SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Триггер очереди, поисковый идентификатор из JSON (JavaScript)

В следующем примере показана входная привязка Cosmos DB в файле *function.json* и функция [JavaScript](functions-reference-node.md), которая использует привязку. Функция считывает один документ и обновляет текстовое значение в документе.

Данные привязки в файле *function.json*:

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>Триггер HTTP, поисковый идентификатор из строки запроса (JavaScript)

В следующем примере показана [функция JavaScript](functions-reference-node.md), которая получает один документ. The function is triggered by an HTTP request that uses a query string to specify the ID and partition key value to look up. That ID and partition key value are used to retrieve a `ToDoItem` document from the specified database and collection.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>Триггер HTTP, поисковый идентификатор из данных маршрута (JavaScript)

В следующем примере показана [функция JavaScript](functions-reference-node.md), которая получает один документ. The function is triggered by an HTTP request that uses a query string to specify the ID and partition key value to look up. That ID and partition key value are used to retrieve a `ToDoItem` document from the specified database and collection.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Триггер очереди, получение нескольких документов, используется SqlQuery (JavaScript)

В следующем примере показана входная привязка Azure Cosmos DB в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку. Функция извлекает несколько документов, указанных SQL-запросом, используя триггер очереди для настройки параметров запроса.

Триггер очереди предоставляет параметр `departmentId`. Сообщение из очереди `{ "departmentId" : "Finance" }` возвратит все записи для финансового отдела.

Данные привязки в файле *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

[Пропустить примеры входных данных](#input---attributes)

### <a name="input---python-examples"></a>Примеры Python: входные данные

В этом разделе содержатся следующие примеры, в которых один документ считывается при указании значения идентификатора из разных источников:

* [Триггер очереди, поисковый идентификатор из JSON](#queue-trigger-look-up-id-from-json-python)
* [Триггер HTTP, поисковый идентификатор из строки запроса](#http-trigger-look-up-id-from-query-string-python)
* [Триггер HTTP, поисковый идентификатор из данных маршрута](#http-trigger-look-up-id-from-route-data-python)
* [Триггер очереди, получение нескольких документов, используется SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-python"></a>Триггер очереди, поисковый идентификатор из JSON (Python)

В следующем примере показаны привязка Cosmos DB в файле *function.json* и [функция Python](functions-reference-python.md), которая использует эту привязку. Функция считывает один документ и обновляет текстовое значение в документе.

Данные привязки в файле *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже приведен код Python.

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-python"></a>Триггер HTTP, поисковый идентификатор из строки запроса (Python)

В следующем примере показана [функция Python](functions-reference-python.md), которая получает один документ. The function is triggered by an HTTP request that uses a query string to specify the ID and partition key value to look up. That ID and partition key value are used to retrieve a `ToDoItem` document from the specified database and collection.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

Ниже приведен код Python.

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-python"></a>Триггер HTTP, поисковый идентификатор из данных маршрута (Python)

В следующем примере показана [функция Python](functions-reference-python.md), которая получает один документ. The function is triggered by an HTTP request that uses a query string to specify the ID and partition key value to look up. That ID and partition key value are used to retrieve a `ToDoItem` document from the specified database and collection.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Ниже приведен код Python.

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>Триггер очереди, получение нескольких документов, использование SqlQuery (Python)

В следующем примере показана входная привязка Azure Cosmos DB в файле *function.json* и [функция Python](functions-reference-python.md), которая использует эту привязку. Функция извлекает несколько документов, указанных SQL-запросом, используя триггер очереди для настройки параметров запроса.

Триггер очереди предоставляет параметр `departmentId`. Сообщение из очереди `{ "departmentId" : "Finance" }` возвратит все записи для финансового отдела.

Данные привязки в файле *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже приведен код Python.

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```


[Пропустить примеры входных данных](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Примеры входных данных F#

В следующем примере показаны привязка Cosmos DB в файле *function.json* и [функция F#](functions-reference-fsharp.md), которая использует эту привязку. Функция считывает один документ и обновляет текстовое значение в документе.

Данные привязки в файле *function.json*:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже показан код F#.

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Для этого примера нужен файл `project.json`, указывающий зависимости NuGet `FSharp.Interop.Dynamic` и `Dynamitey`:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Чтобы добавить файл `project.json`, см. раздел [об управлении пакетом F#](functions-reference-fsharp.md#package).

### <a name="input---java-examples"></a>Примеры входных данных Java

В этом разделе содержатся следующие примеры:

* [Триггер HTTP, поисковый идентификатор из строки запроса — строковый параметр](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Триггер HTTP, поисковый идентификатор из строки запроса — параметр POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Триггер HTTP, поисковый идентификатор из данных маршрута](#http-trigger-look-up-id-from-route-data-java)
* [Триггер HTTP, поисковый идентификатор из данных маршрута, используется SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Триггер HTTP, получение нескольких документов из данных маршрута, используется SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

В примерах используется `ToDoItem` простого типа:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

#### <a name="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>Триггер HTTP, поисковый идентификатор из строки запроса — строковый параметр (Java)

В следующем примере показана функция Java, которая получает один документ. The function is triggered by a HTTP request that uses a query string to specify the ID and partition key value to look up. That ID and partition key value are used to retrieve a document from the specified database and collection, in String form.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@CosmosDBInput` для параметров функции, значения которых будут поступать из Cosmos DB.  This annotation can be used with native Java types, POJOs, or nullable values using Optional\<T>.

#### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>Триггер HTTP, поисковый идентификатор из строки запроса — параметр POJO (Java)

В следующем примере показана функция Java, которая получает один документ. The function is triggered by a HTTP request that uses a query string to specify the ID and partition key value to look up. That ID and partition key value used to retrieve a document from the specified database and collection. Затем документ преобразуется в ранее созданный экземпляр POJO ```ToDoItem``` и передается функции в качестве аргумента.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

#### <a name="http-trigger-look-up-id-from-route-data-java"></a>Триггер HTTP, поисковый идентификатор из данных маршрута (Java)

В следующем примере показана функция Java, которая получает один документ. The function is triggered by a HTTP request that uses a route parameter to specify the ID and partition key value to look up. That ID and partition key value are used to retrieve a document from the specified database and collection, returning it as an ```Optional<String>```.

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>Триггер HTTP, поисковый идентификатор из данных маршрута, используется SqlQuery (Java)

В следующем примере показана функция Java, которая получает один документ. Функция инициируется HTTP-запросом, в параметре маршрута которого указан идентификатор для поиска. Этот идентификатор используется для получения документа из указанной базы данных и коллекции. При этом результирующий набор преобразуется в ```ToDoItem[]```, так как может быть возвращено несколько документов в зависимости от критериев запроса.

> [!NOTE]
> If you need to query by just the ID, it is recommended to use a look up, like the [previous examples](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), as it will consume less [request units](../cosmos-db/request-units.md). Point read operations (GET) are [more efficient](../cosmos-db/optimize-cost-queries.md) than queries by ID.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

#### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>Триггер HTTP, получение нескольких документов из данных маршрута, используется SqlQuery (Java)

В следующем примере показана функция Java, которая получает несколько документов. Функция инициируется HTTP-запросом, в параметре маршрута ```desc``` которого указана строка для поиска в поле ```description```. Поисковый запрос используется для получения коллекции документов из указанной базы данных и коллекции. При этом результирующий набор преобразуется в ```ToDoItem[]``` и передается функции в качестве аргумента.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

## <a name="input---attributes"></a>Входные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs).

Конструктор атрибута принимает имя базы данных и имя коллекции. Дополнительные сведения о параметрах и других свойствах, которые можно настроить, см. в следующем разделе о [настройке](#input---configuration).

## <a name="input---configuration"></a>Входная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `CosmosDB`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type**     || Нужно задать значение `cosmosDB`.        |
|**direction**     || Нужно задать значение `in`.         |
|**name**     || Имя параметра привязки, представляющего документ в функции.  |
|**databaseName** |**DatabaseName** |База данных, содержащая документ.        |
|**collectionName** |**CollectionName** | Имя коллекции, содержащей документ. |
|**id**    | **Id** | Идентификатор документа, который нужно получить. Это свойство поддерживает [выражения привязок](./functions-bindings-expressions-patterns.md). Не задавайте свойства **id** или **sqlQuery** одновременно. Если не задать ни одного из них, извлекается вся коллекция. |
|**sqlQuery**  |**SqlQuery**  | SQL-запрос к Azure Cosmos DB, используемый для извлечения нескольких документов. Свойство поддерживает привязки времени выполнения, как показано в примере: `SELECT * FROM c where c.departmentId = {departmentId}`. Не задавайте свойства **id** или **sqlQuery** одновременно. Если не задать ни одного из них, извлекается вся коллекция.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Имя параметра приложения, содержащего строку подключения к Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Задает значение ключа секции для поиска. Может включать параметры привязки. It is required for lookups in [partitioned](../cosmos-db/partition-data.md#logical-partitions) collections.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Использование входной привязки

В функциях C# и F# любые изменения, внесенные во входной документ посредством именованных входных параметров, сохраняются автоматически после успешного выхода из функции.

В функциях JavaScript изменения не обрабатываются автоматически при выходе из функции. Для внесения изменений используйте `context.bindings.<documentName>In` и `context.bindings.<documentName>Out`. Ознакомьтесь с примером на языке JavaScript.

## <a name="output"></a>Выходные данные

Выходная привязка Azure Cosmos DB позволяет записать новый документ в базу данных Azure Cosmos DB с помощью API SQL.

## <a name="output---examples"></a>Примеры выходных данных

Языковой пример см. в разделах:

* [C#](#output---c-examples)
* [Сценарий C# (CSX)](#output---c-script-examples)
* [F#](#output---f-examples)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-examples)
* [Python](#output---python-examples)

См. также [пример входных данных](#input---c-examples), в котором используется `DocumentClient`.

[Пропустить примеры вывода](#output---attributes)

### <a name="output---c-examples"></a>Примеры выходных данных C#

В этом разделе содержатся следующие примеры:

* Триггер очереди, запись одного документа
* Триггер очереди, запись документов при помощи IAsyncCollector

В примерах используется `ToDoItem` простого типа:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Пропустить примеры вывода](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Триггер очереди, запись одного документа (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая добавляет документ в базу данных, используя данные, полученные из сообщения хранилища очередей.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

[Пропустить примеры вывода](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Триггер очереди, запись документов с помощью IAsyncCollector (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая добавляет коллекцию документов в базу данных с помощью данных, полученных из JSON сообщения очереди.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

[Пропустить примеры вывода](#output---attributes)

### <a name="output---c-script-examples"></a>Примеры выходных данных скрипта C#

В этом разделе содержатся следующие примеры:

* Триггер очереди, запись одного документа
* Триггер очереди, запись документов при помощи IAsyncCollector

[Пропустить примеры вывода](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Триггер очереди, запись одного документа (скрипт C#)

В следующем примере показаны выходная привязка Azure Cosmos DB в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция использует входную привязку очереди для очереди, которую получает JSON в следующем формате:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Функция создает документы Azure Cosmos DB для каждой записи в следующем формате:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Данные привязки в файле *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Триггер очереди, запись документов при помощи IAsyncCollector

Для создания нескольких документов можно выполнить привязку к `ICollector<T>` или к `IAsyncCollector<T>`, где `T` — любой из поддерживаемых типов.

Этот пример относится к простому типу `ToDoItem`:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Ниже показан файл function.json:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Ниже приведен код скрипта C#.

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

[Пропустить примеры вывода](#output---attributes)

### <a name="output---javascript-examples"></a>Примеры выходных данных JavaScript

В следующем примере показана выходная привязка Azure Cosmos DB в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Функция использует входную привязку очереди для очереди, которую получает JSON в следующем формате:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Функция создает документы Azure Cosmos DB для каждой записи в следующем формате:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Данные привязки в файле *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

[Пропустить примеры вывода](#output---attributes)

### <a name="output---f-examples"></a>Примеры выходных данных F#

В следующем примере показаны выходная привязка Azure Cosmos DB в файле *function.json* и [функция F#](functions-reference-fsharp.md), которая использует эту привязку. Функция использует входную привязку очереди для очереди, которую получает JSON в следующем формате:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Функция создает документы Azure Cosmos DB для каждой записи в следующем формате:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Данные привязки в файле *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже показан код F#.

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json
    open Microsoft.Extensions.Logging

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: ILogger) =
      log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Для этого примера нужен файл `project.json`, указывающий зависимости NuGet `FSharp.Interop.Dynamic` и `Dynamitey`:

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Чтобы добавить файл `project.json`, см. раздел [об управлении пакетом F#](functions-reference-fsharp.md#package).

### <a name="output---java-examples"></a>Примеры выходных данных Java

* [Триггер очереди, сохранение сообщения в базе данных через возвращаемое значение](#queue-trigger-save-message-to-database-via-return-value-java)
* [Триггер HTTP, сохранение одного документа в базе данных через возвращаемое значение](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Триггер HTTP, сохранение одного документа в базе данных через OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Триггер HTTP, сохранение нескольких документов в базе данных через OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


#### <a name="queue-trigger-save-message-to-database-via-return-value-java"></a>Триггер очереди, сохранение сообщения в базе данных через возвращаемое значение (Java)

В следующем примере показана функция Java, которая добавляет документ в базу данных, используя данные из сообщения в хранилище очередей.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```

#### <a name="http-trigger-save-one-document-to-database-via-return-value-java"></a>Триггер HTTP, сохранение одного документа в базе данных через возвращаемое значение (Java)

В следующем примере показана функция Java, подпись которой помечена с помощью аннотации ```@CosmosDBOutput``` и которая возвращает значение типа ```String```. Документ JSON, возвращаемый функцией, автоматически записывается в соответствующую коллекцию CosmosDB.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

#### <a name="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>Триггер HTTP, сохранение одного документа в базе данных через OutputBinding (Java)

В следующем примере показана функция Java, которая записывает документ в CosmosDB с помощью выходного параметра ```OutputBinding<T>```. Обратите внимание, что в этом случае аннотацию ```@CosmosDBOutput``` необходимо добавить к параметру ```outputItem```, а не к подписи функции. ```OutputBinding<T>``` позволяет использовать привязку для записи документа в CosmosDB в вашей функции, а также позволяет возвращать вызывающему объекту другое значение, например JSON или XML-документ.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

#### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>Триггер HTTP, сохранение нескольких документов в базе данных через OutputBinding (Java)

В следующем примере показана функция Java, которая записывает несколько документов в CosmosDB с помощью выходного параметра ```OutputBinding<T>```. Обратите внимание, что в этом случае аннотацию ```@CosmosDBOutput``` необходимо добавить к параметру ```outputItem```, а не к подписи функции. Выходной параметр ```outputItem``` содержит список объектов ```ToDoItem``` в качестве типа параметра шаблона. ```OutputBinding<T>``` позволяет использовать привязку для записи документов в CosmosDB в вашей функции, а также позволяет возвращать вызывающему объекту другое значение, например JSON или XML-документ.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@CosmosDBOutput` для параметров, которые будут записываться в Cosmos DB.  Необходимо использовать тип параметра аннотации ```OutputBinding<T>```, где T — собственный тип Java или POJO.

### <a name="output---python-examples"></a>Output - Python examples

The following example demonstrates how to write a document to an Azure CosmosDB database as the output of a function.

The binding definition is defined in *function.json* where *type* is set to `cosmosDB`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

To write to the database, pass a document object to the `set` method of the database parameter.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

## <a name="output---attributes"></a>Выходные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs).

Конструктор атрибута принимает имя базы данных и имя коллекции. Дополнительные сведения о параметрах и других свойствах, которые можно настроить, см. в разделе [Выходная конфигурация](#output---configuration). Ниже приведен пример атрибута `CosmosDB` в сигнатуре метода:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Полный пример см. в разделе "Пример выходных данных C#".

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `CosmosDB`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type**     || Нужно задать значение `cosmosDB`.        |
|**direction**     || Нужно задать значение `out`.         |
|**name**     || Имя параметра привязки, представляющего документ в функции.  |
|**databaseName** | **DatabaseName**|База данных, содержащая коллекцию, в которой создается документ.     |
|**collectionName** |**CollectionName**  | Имя коллекции, в которой создается документ. |
|**createIfNotExists**  |**CreateIfNotExists**    | Логическое значение, указывающее, будет ли создана коллекция при ее отсутствии. Значение по умолчанию — *false*, так как коллекции создаются с использованием зарезервированной пропускной способности, с которой связаны ценовые требования. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Если для `CreateIfNotExists` задано значение true, определяется путь к ключу раздела для созданной коллекции.|
|**CollectionThroughput**|**CollectionThroughput**| Если для `CreateIfNotExists` задано значение true, определяется [пропускная способность](../cosmos-db/set-throughput.md) созданной коллекции.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Имя параметра приложения, содержащего строку подключения к Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

При записи в параметре вывода функции в базе данных по умолчанию создается документ, для которого автоматически создается GUID в качестве идентификатора документа. Идентификатор выходного документа можно определить, указав свойство `id` в объекте JSON, передаваемом в параметр вывода.

> [!Note]
> Если указать идентификатор существующего документа, он перезаписывается новым выходным документом.

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочные материалы |
|---|---|
| Cosmos DB; | [Коды ошибок Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Параметры файла host.json

В этом разделе описываются глобальные параметры конфигурации, доступные для этой привязки в версии 2.x. Дополнительные сведения о глобальных параметрах конфигурации в версии 2.x см. в статье [Справочник по файлу host.json для Функций Azure](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Свойство  |значение по умолчанию | Описание |
|---------|---------|---------|
|GatewayMode|Шлюз|Режим подключения, используемый функцией при подключении к службе Azure Cosmos DB. Возможные значения: `Direct` и `Gateway`.|
|Протокол|Https|Протокол подключения, используемый функцией при подключении к службе Azure Cosmos DB.  [Описание обоих режимов](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|Н/Д|Префикс аренды для использования во всех функциях приложения.|

## <a name="next-steps"></a>Дальнейшие действия

* [Azure Cosmos DB: обработка данных бессерверных баз данных с помощью службы "Функции Azure"](../cosmos-db/serverless-computing-database.md)
* [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
