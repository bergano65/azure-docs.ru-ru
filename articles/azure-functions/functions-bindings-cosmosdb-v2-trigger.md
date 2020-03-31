---
title: Спусковой крючок Azure Cosmos DB для функций 2.x
description: Научитесь использовать спусковой крючок Azure Cosmos DB в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277574"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Спусковой крючок Azure Cosmos DB для Azure Functions 2.x

Триггер Azure Cosmos DB использует [канал изменений Azure Cosmos DB](../cosmos-db/change-feed.md) для ожидания вставок и обновлений в разных разделах. На канале изменений публикуются вставки и обновления, а не удаления.

Для получения информации о настройке и деталях конфигурации, [см.](./functions-bindings-cosmosdb-v2.md)

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В следующем примере показаны привязка триггера Cosmos DB в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция записывает сообщения журнала при добавлении или изменении записей Cosmos DB.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

В следующем примере показаны привязка триггера Cosmos DB в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Функция записывает сообщения журнала при добавлении или изменении записей Cosmos DB.

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

Эта функция вызывается при наличии вставок или обновлений в указанной базе данных и коллекции.

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


В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@CosmosDBTrigger` для параметров, значения которых будут поступать из Cosmos DB.  Эта заметка может использоваться с собственными типами Java, объектами POJO или значениями, допускающими значения NULL, используя `Optional<T>`.

---

## <a name="attributes-and-annotations"></a>Атрибуты и аннотации

# <a name="c"></a>[C #](#tab/csharp)

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs).

Конструктор атрибута принимает имя базы данных и имя коллекции. Дополнительные сведения о параметрах и других свойствах, которые можно настроить, см. в разделе [Конфигурация триггера](#configuration). Ниже приведен пример атрибута `CosmosDBTrigger` в сигнатуре метода:

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

Для полного примера [см.](#example)

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Атрибуты не поддерживаются скриптом C'.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Атрибуты не поддерживаются JavaScript.

# <a name="python"></a>[Python](#tab/python)

Атрибуты не поддерживаются Python.

# <a name="java"></a>[Java](#tab/java)

Из [библиотеки выполнения функций](https://docs.microsoft.com/java/api/overview/azure/functions/runtime) `@CosmosDBInput` Java используйте аннотацию по параметрам, считывае данным из Cosmos DB.

---

## <a name="configuration"></a>Параметр Configuration

В следующей таблице объясняется свойства связывающей конфигурации, `CosmosDBTrigger` установленные в файле *function.json* и атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**тип** | Недоступно | Нужно задать значение `cosmosDBTrigger`. |
|**direction** | Недоступно | Нужно задать значение `in`. Этот параметр задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, используемое в коде функции, представляющей список документов с изменениями. |
|**подключениеСтнетНастройка**|**ConnectionStringSetting** | Имя параметра приложения, содержащего строку подключения, используемую для подключения к отслеживаемой учетной записи Azure Cosmos DB. |
|**databaseName**|**Databasename**  | Имя базы данных Azure Cosmos DB с отслеживаемой коллекцией. |
|**сборИмя** |**CollectionName** | Имя отслеживаемой коллекции. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Необязательно) Название параметра приложения, содержащего строку подключения к учетной записи Azure Cosmos DB, в которой хранится коллекция аренды. Если значение не задано, используется значение `connectionStringSetting`. Этот параметр задается автоматически при создании привязки на портале. Строка подключения для коллекции аренд должна иметь разрешения на запись.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Необязательно.) Имя базы данных, в которой содержится коллекция, используемая для хранения аренд. Если значение не задано, используется значение параметра `databaseName`. Этот параметр задается автоматически при создании привязки на портале. |
|**leaseCollectionNameName** | **LeaseCollectionName** | (Необязательно.) Имя коллекции, используемой для хранения аренд. Если значение не задано, используется значение `leases`. |
|**createLeaseCollectionifNotExists** | **CreateLeaseCollectionIfNotExists** | (Необязательно.) Если задано значение `true`, коллекция аренд создается автоматически, если она не создана. Значение по умолчанию — `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Необязательно) Определяет количество единиц запроса для назначения при создании собрания договоров аренды. Эта настройка используется `createLeaseCollectionIfNotExists` только `true`при установке. Этот параметр задается автоматически при создании привязки с помощью портала.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Необязательно) При установке значение добавляется в качестве приставки к аренде, созданной в коллекции Lease для этой функции. Использование префикса позволяет двум отдельным функциям Azure обмениваться одной и той же коллекцией lease, используя различные префиксы.
|**feedPollDelay**| **FeedPollDelay**| (Необязательно) Время (в миллисекундах) для задержки между опросом раздела для новых изменений на канале, после того, как все текущие изменения истощаются. По умолчанию 5000 миллисекунд, или 5 секунд.
|**арендаAcquireInterval**| **LeaseAcquireInterval**| (Дополнительно) Если параметр задан, то он определяет интервал для запуска задачи вычисления при условии равномерности распределения секций между известными экземплярами узла (в миллисекундах). По умолчанию это 13000 (13 секунд).
|**арендаЭкспирацияInterval**| **LeaseExpirationInterval**| (Дополнительно) Если параметр задан, то он определяет интервал, за который берется аренда, представляющая секцию (в миллисекундах). Если аренда не будет обновлена в течение этого интервала, это приведет к ее истечению и владение секцией перейдет к другому экземпляру. По умолчанию это 60000 (60 секунд).
|**арендаRenewInterval**| **LeaseRenewInterval**| (Дополнительно) Если параметр задан, то он определяет интервал продления для всех аренд в разделах, которые на данный момент занятые экземплярами (в миллисекундах). По умолчанию это 17000 (17 секунд).
|**Контрольно-пропускной пунктЧастота**| **CheckpointFrequency**| (Дополнительно) Если параметр задан, то он определяет интервал между контрольными точками аренды (в миллисекундах). После каждого вызова функции всегда используется значение по умолчанию.
|**maxItemsPerInvocation**| **MaxitemsperInvocation**| (Необязательно) При наборе это свойство устанавливает максимальное количество элементов, полученных на вызов функции. Если операции в контролируемой коллекции выполняются с помощью сохраненных процедур, [область действия транзакций](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) сохраняется при чтении элементов из ленты изменений. В результате количество полученных элементов может быть выше указанного значения, так что элементы, измененные одной и той же транзакцией, возвращаются в составе одной атомной партии.
|**startFromStart**| **StartFromBeginning**| (Необязательно) Эта опция говорит триггеру читать изменения с начала истории изменения коллекции вместо того, чтобы начинать в текущем времени. Чтение с самого начала работает только при первом запуске триггера, так как в последующих запусках контрольно-пропускные пункты уже сохраняются. Установка этой `true` опции на время аренды уже создана не имеет эффекта. |
|**предпочтительныеЛокации**| **ПредпочтительныеМеста**| (Необязательно) Определяет предпочтительные местоположения (регионы) для геореплицированных учетных записей баз данных в службе Azure Cosmos DB. Значения должны быть разделены запятой. Например, "Восточные США, южная центральная часть США, Северная Европа". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Использование

Триггеру требуется вторая коллекция, которая используется для хранения _аренды_ в разделах. Отслеживаемая коллекция и та, в которой содержатся аренды, должны быть доступны для успешной работы триггера.

>[!IMPORTANT]
> Если несколько функций настроены на использование триггера Cosmos DB для одной и той же коллекции, то каждая из функций должна использовать выделенную коллекцию аренды или указывать на другой префикс `LeaseCollectionPrefix` для каждой функции. В противном случае активируется только одна из функций. Сведения о префиксе см. в разделе с [конфигурацией](#configuration).

Триггер не указывает, был ли документ обновлен или вставлен. Вместо этого он представляет сам документ. Если операции обновления и вставки необходимо обрабатывать по-разному, это можно сделать, внедрив поля меток времени для операций обновления и вставки.

## <a name="next-steps"></a>Дальнейшие действия

- [Прочитайте документ Azure Cosmos DB (связывание ввода)](./functions-bindings-cosmosdb-v2-input.md)
- [Сохранение изменений в документе Azure Cosmos DB (связка выхода)](./functions-bindings-cosmosdb-v2-output.md)