---
title: Привязки хранилища таблиц Azure для службы "Функции Azure"
description: Узнайте, как использовать привязки службы хранилища таблиц Azure в службе "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 50706e1b525a3e3a39701ef2135d44c02c35077e
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181142"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Привязки хранилища таблиц Azure для службы "Функции Azure"

В этой статье рассматривается работа с привязками хранилища таблиц Azure в Функциях Azure. Функции Azure поддерживают входные и выходные привязки для хранилища таблиц Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Привязки службы "Хранилище таблиц" доступны в пакете NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) версии 2.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages — функции 2. x и более поздних версий

Привязки хранилища таблиц доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) версии 3.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Ввод

Используйте входную привязку хранилища таблиц Azure для чтения таблицы в учетной записи хранения Azure.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Одна сущность

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая считывает одну строку таблицы. Для каждого сообщения, отправляемого в очередь, будет активирована функция.

Значение ключа строки {queueTrigger} указывает, что ключ строки получен из строки сообщения очереди.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

В следующем примере показана [функция C#](functions-dotnet-class-library.md) , которая считывает несколько строк таблицы, `MyPoco` от которых наследуется класс `TableEntity` .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` поддерживается только в [среде выполнения функций v1](functions-versions.md). Альтернативой является использование параметра метода `CloudTable` для чтения таблицы с помощью пакета SDK службы хранилища Azure. Ниже приведен пример функции, которая запрашивает таблицу журнала функций Azure:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Дополнительную информацию об использовании CloudTable см. в статье [Начало работы с хранилищем таблиц Azure и API таблиц Azure Cosmos DB с помощью .NET](../cosmos-db/tutorial-develop-table-dotnet.md).

Если при попытке привязать к `CloudTable` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

### <a name="one-entity"></a>Одна сущность

В следующем примере показана входная привязка таблицы в файле *function.json* и коде [скрипта C#](functions-reference-csharp.md), который использует привязку. Функция использует триггер очереди для чтения одной строки таблицы. 

Файл *function.json* определяет `partitionKey` и `rowKey`. Значение `rowKey` "{queueTrigger}" указывает, что ключ строки получен из строки сообщения очереди.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

В следующем примере показана входная привязка таблицы в файле *function.json* и коде [скрипта C#](functions-reference-csharp.md), который использует привязку. Функция считывает сущности ключа раздела, который указан в очереди сообщений.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

В коде скрипта C# добавляется ссылка на пакет SDK для службы хранилища Azure, чтобы тип сущности мог быть производным от `TableEntity`.

```csharp
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` не поддерживается в среде выполнения функций для [версий 2. x и выше](functions-versions.md). Альтернативой является использование параметра метода `CloudTable` для чтения таблицы с помощью пакета SDK службы хранилища Azure. Ниже приведен пример функции, которая запрашивает таблицу журнала функций Azure:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Дополнительную информацию об использовании CloudTable см. в статье [Начало работы с хранилищем таблиц Azure и API таблиц Azure Cosmos DB с помощью .NET](../cosmos-db/tutorial-develop-table-dotnet.md).

Если при попытке привязать к `CloudTable` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[JavaScript](#tab/javascript)

В следующем примере показана входная привязка таблицы в файле *function.json* и [код JavaScript](functions-reference-node.md), который использует привязку. Функция использует триггер очереди для чтения одной строки таблицы. 

Файл *function.json* определяет `partitionKey` и `rowKey`. Значение `rowKey` "{queueTrigger}" указывает, что ключ строки получен из строки сообщения очереди.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Одна строка таблицы 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

В следующем примере показана функция, активируемая HTTP, которая возвращает список объектов Person, которые находятся в указанном разделе в хранилище таблиц. В этом примере ключ секции извлекается из маршрута HTTP, а tableName и Connection — из параметров функции. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

Заметка Таблеинпут также может извлекать привязки из тела запроса JSON, как показано в следующем примере.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

В следующих примерах фильтр используется для запроса лиц с указанным именем в таблице Azure и ограничивает количество возможных совпадений до 10 результатов.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Входные атрибуты и заметки

# <a name="c"></a>[C#](#tab/csharp)

 В [библиотеках класса C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки входной привязки таблицы:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Конструктор атрибута принимает имя таблицы, ключ раздела и строки. Атрибут может использоваться для `out` параметра или возвращаемого значения функции, как показано в следующем примере:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Полный пример см. в разделе примеров входных данных C#.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Предоставляет еще один способ указать используемую учетную запись хранения. Конструктор принимает имя параметра приложения, содержащего строку подключения к службе хранилища. Атрибут может применяться на уровне класса, метода или параметра. Ниже показан пример уровня класса и метода.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Используемая учетная запись хранения определяется в следующем порядке:

* Свойство `Table` атрибута `Connection`.
* Атрибут `StorageAccount`, примененный к тому же параметру, что и `Table`.
* Атрибут `StorageAccount`, примененный к функции.
* Атрибут `StorageAccount`, примененный к классу.
* Учетная запись хранения по умолчанию для приложения-функции (параметр приложения AzureWebJobsStorage).

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В скрипте C# атрибуты не поддерживаются.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

В JavaScript атрибуты не поддерживаются.

# <a name="python"></a>[Python](#tab/python)

В Python атрибуты не поддерживаются.

# <a name="java"></a>[Java](#tab/java)

В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@TableInput` для параметров, значения которых будут поступать из Хранилища таблиц.  Эта заметка может использоваться с собственными типами Java, объектами POJO или значениями, допускающими значения NULL, используя `Optional<T>`.

---

## <a name="input---configuration"></a>Входная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Table`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `table`. Это свойство задается автоматически при создании привязки на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `in`. Это свойство задается автоматически при создании привязки на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей таблицу или сущность в коде функции. | 
|**tableName** | **TableName** | Имя таблицы.| 
|**partitionKey** | **PartitionKey** |Необязательный элемент. Ключ раздела сущности таблицы, которую нужно считать. Инструкции по использованию этого свойства см. в разделе [Использование](#input---usage) .| 
|**rowKey** |**RowKey** | Необязательный элемент. Ключ строки сущности таблицы, которую нужно считать. Инструкции по использованию этого свойства см. в разделе [Использование](#input---usage) .| 
|**take** |**Take** | Необязательный элемент. Максимальное количество считываемых сущностей в JavaScript. Инструкции по использованию этого свойства см. в разделе [Использование](#input---usage) .| 
|**filter** |**Filter** | Необязательный элемент. Выражение фильтра OData для входных данных таблицы в JavaScript. Инструкции по использованию этого свойства см. в разделе [Использование](#input---usage) .| 
|**connection**; |**Соединение** | Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Параметр может быть именем предопределенного параметра приложения "AzureWebJobs" или именем строки подключения. Например, если имя параметра — "Азуревебжобсмистораже", можно указать "MyStorage" здесь. Среда выполнения функций будет автоматически искать параметр приложения с именем «Азуревебжобсмистораже». Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Использование входной привязки

# <a name="c"></a>[C#](#tab/csharp)

* **Чтение одной строки в**

  Задайте значения для `partitionKey` и `rowKey`. Получите доступ к данным таблицы с помощью параметра метода `T <paramName>`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. `T` обычно представляет собой тип, реализующий `ITableEntity`, или является производным от `TableEntity`. Свойства `filter` и `take` не используются в этом сценарии.

* **Чтение одной или нескольких строк**

  Получите доступ к данным таблицы с помощью параметра метода `IQueryable<T> <paramName>`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. `T` должен представлять собой тип, реализующий `ITableEntity`, или быть производным от `TableEntity`. Для выполнения фильтрации можно использовать методы `IQueryable`. Свойства `partitionKey`, `rowKey`, `filter` и `take` не используются в этом сценарии.  

  > [!NOTE]
  > `IQueryable` не поддерживается в [среде выполнения Функций версии 2](functions-versions.md). Альтернативой является [использование параметра метода CloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) для чтения таблицы с помощью пакета SDK службы хранилища Azure. Если при попытке привязать к `CloudTable` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

* **Чтение одной строки в**

  Задайте значения для `partitionKey` и `rowKey`. Получите доступ к данным таблицы с помощью параметра метода `T <paramName>`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. `T` обычно представляет собой тип, реализующий `ITableEntity`, или является производным от `TableEntity`. Свойства `filter` и `take` не используются в этом сценарии.

* **Чтение одной или нескольких строк**

  Получите доступ к данным таблицы с помощью параметра метода `IQueryable<T> <paramName>`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. `T` должен представлять собой тип, реализующий `ITableEntity`, или быть производным от `TableEntity`. Для выполнения фильтрации можно использовать методы `IQueryable`. Свойства `partitionKey`, `rowKey`, `filter` и `take` не используются в этом сценарии.  

  > [!NOTE]
  > `IQueryable` не поддерживается в [среде выполнения Функций версии 2](functions-versions.md). Альтернативой является [использование параметра метода CloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) для чтения таблицы с помощью пакета SDK службы хранилища Azure. Если при попытке привязать к `CloudTable` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Укажите свойства `filter` и `take`. Не определяйте свойства `partitionKey` и `rowKey`. Получите доступ к сущности (или сущностям) входной таблицы, используя `context.bindings.<BINDING_NAME>`. Десериализированный объект имеет свойства `RowKey` и `PartitionKey`.

# <a name="python"></a>[Python](#tab/python)

Табличные данные передаются в функцию в виде строки JSON. Отмените сериализацию сообщения, вызвав, `json.loads` как показано во входном [примере](#input).

# <a name="java"></a>[Java](#tab/java)

Атрибут [таблеинпут](/java/api/com.microsoft.azure.functions.annotation.tableinput) предоставляет доступ к строке таблицы, которая активировала функцию.

---

## <a name="output"></a>Выходные данные

Используйте выходную привязку хранилища таблиц Azure для записи сущностей в таблицу в учетной записи хранения Azure.

> [!NOTE]
> Выходная привязка не поддерживает обновление существующих сущностей. Для обновления существующей сущности используйте операцию `TableOperation.Replace` из [пакета SDK для службы хранилища Azure](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity).

# <a name="c"></a>[C#](#tab/csharp)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая использует триггер HTTP для записи одной строки таблицы. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В следующем примере показана выходная привязка таблицы в файле *function.json* и коде [скрипта C#](functions-reference-csharp.md), который использует привязку. Эта функция записывает несколько сущностей в таблице.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

В следующем примере показана выходная привязка таблицы в файле *function.json* и функции [JavaScript](functions-reference-node.md), которая использует привязку. Эта функция записывает несколько сущностей в таблице.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

В следующем примере показано, как использовать выходную привязку хранилища таблиц. `table`Привязка настраивается в *function.jsв* , присваивая значения `name` , `tableName` , `partitionKey` и `connection` :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Следующая функция создает уникальный УУИ для `rowKey` значения и сохраняет сообщение в хранилище таблиц.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

В следующем примере показана функция Java, использующая триггер HTTP для записи одной строки таблицы.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

В следующем примере показана функция Java, использующая триггер HTTP для записи нескольких строк таблицы.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Выходные атрибуты и заметки

# <a name="c"></a>[C#](#tab/csharp)

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Конструктор атрибута использует имя таблицы. Атрибут может использоваться для `out` параметра или возвращаемого значения функции, как показано в следующем примере:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Полный пример см. в разделе [пример выходных данных C#](#output).

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Дополнительные сведения см. в разделе [Входные атрибуты](#input---attributes-and-annotations).

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В скрипте C# атрибуты не поддерживаются.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

В JavaScript атрибуты не поддерживаются.

# <a name="python"></a>[Python](#tab/python)

В Python атрибуты не поддерживаются.

# <a name="java"></a>[Java](#tab/java)

В [библиотеке времени выполнения функций Java](/java/api/overview/azure/functions/runtime)используйте аннотацию [таблеаутпут](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) в параметрах для записи значений в хранилище таблиц.

[Дополнительные сведения](#output)см. в примере.

---

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Table`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `table`. Это свойство задается автоматически при создании привязки на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `out`. Это свойство задается автоматически при создании привязки на портале Azure. |
|**name** | Недоступно | Имя переменной, используемое в функции кода, которая представляет таблицу или сущность. Задайте значение `$return`, ссылающееся на возвращаемое значение функции.| 
|**tableName** |**TableName** | Имя таблицы.| 
|**partitionKey** |**PartitionKey** | Ключ раздела сущности таблицы, которую нужно записать. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#output---usage).| 
|**rowKey** |**RowKey** | Ключ строки сущности таблицы, которую нужно записать. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#output---usage).| 
|**connection**; |**Соединение** | Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задано значение `connection` "MyStorage", среда выполнения функций ищет параметр приложения с именем "MyStorage". Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

# <a name="c"></a>[C#](#tab/csharp)

Получить доступ к сущности выходной таблицы можно с помощью параметра `ICollector<T> paramName` метода `IAsyncCollector<T> paramName` или `T` , где `PartitionKey` включает `RowKey` Свойства и. Эти свойства часто сопровождаются реализацией `ITableEntity` или наследованием `TableEntity` .

Кроме того, можно использовать `CloudTable` параметр метода для записи в таблицу с помощью пакета SDK службы хранилища Azure. Если при попытке привязать к `CloudTable` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Получить доступ к сущности выходной таблицы можно с помощью параметра `ICollector<T> paramName` метода `IAsyncCollector<T> paramName` или `T` , где `PartitionKey` включает `RowKey` Свойства и. Эти свойства часто сопровождаются реализацией `ITableEntity` или наследованием `TableEntity` . `paramName`Значение указывается в `name` свойстве *function.json*.

Кроме того, можно использовать `CloudTable` параметр метода для записи в таблицу с помощью пакета SDK службы хранилища Azure. Если при попытке привязать к `CloudTable` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Доступ к выходному событию с помощью `context.bindings.<name>`, где `<name>` — это значение, указанное в свойстве `name` файла *function.json*.

# <a name="python"></a>[Python](#tab/python)

Существует два варианта вывода сообщения строки табличного хранилища из функции.

- **Возвращаемое значение**: установите `name` свойство в *function.jsна* `$return` . В этой конфигурации возвращаемое значение функции сохраняется как строка хранилища таблицы.

- **Императив**: передайте значение в метод [Set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) параметра, объявленного как тип [out](/python/api/azure-functions/azure.functions.out?view=azure-python) . Значение, передаваемое в `set` , сохраняется как сообщение концентратора событий.

# <a name="java"></a>[Java](#tab/java)

Существует два варианта вывода строки хранилища таблицы из функции с помощью аннотации [таблесторажеаутпут](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) :

- **Возвращаемое значение**: применяя заметку к самой функции, возвращаемое значение функции сохраняется как строка хранилища таблицы.

- **Императивное**: чтобы явно задать значение сообщения, примените заметку к конкретному параметру типа [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , где `T` включает `PartitionKey` Свойства и `RowKey` . Эти свойства часто сопровождаются реализацией `ITableEntity` или наследованием `TableEntity` .

---

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочник |
|---|---|
| Таблица | [Коды ошибок таблицы](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Большой двоичный объект, таблица, очередь | [Коды ошибок хранилища](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Большой двоичный объект, таблица, очередь | [Устранение неполадок](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
