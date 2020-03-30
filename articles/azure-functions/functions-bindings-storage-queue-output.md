---
title: Привязка к выходу хранилища Azure Queue для функций Azure
description: Научитесь создавать сообщения хранения azure Queue в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277340"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Привязка к выходу хранилища Azure Queue для функций Azure

Функции Azure могут создавать новые сообщения хранения очереди Azure, наращая связующее устройство.

Для получения информации о настройке и деталях конфигурации, [см.](./functions-bindings-storage-queue.md)

## <a name="example"></a>Пример

# <a name="c"></a>[C #](#tab/csharp)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая создает сообщения очереди для каждого полученного HTTP-запроса.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В следующем примере показана привязка триггера HTTP в файле *function.json* и коде [сценария C# (CSX)](functions-reference-csharp.md), который использует привязку. Функция создает элемент очереди с полезными данными объекта **CustomQueueMessage** для каждого полученного HTTP-запроса.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

В разделе [Конфигурация](#configuration) описываются эти свойства.

Ниже приведен код скрипта C#, который создает одно сообщение очереди.

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

За один раз можно отправить несколько сообщений с помощью параметра `ICollector` или `IAsyncCollector`. Ниже приведен код скрипта C#, который отправляет несколько сообщений (одно — с данными HTTP-запроса, а другое — с кодовыми значениями).

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

В следующем примере показана привязка триггера HTTP в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку. Эта функция создает элемент очереди для каждого полученного HTTP-запроса.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

В разделе [Конфигурация](#configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Вы можете отправить несколько сообщений одновременно, определив массив сообщений для выходной привязки `myQueueItem`. Следующий код JavaScript отправляет два сообщения очереди с четко фиксированными значениями для каждого полученного HTTP-запроса.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

В следующем примере показано, как вывести одноизавные и несколько значений в очереди хранения. Конфигурация, необходимая для *function.json,* одинакова в любом случае.

Привязка очереди хранения определяется в *function.json,* где *тип* установлен. `queue`

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
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Чтобы установить отдельное сообщение в очереди, `set` вы передаете методу одно значение.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Чтобы создать несколько сообщений в очереди, объявить параметр в качестве соответствующего типа списка `set` и передать методу массив значений (соответствующих типу списка).

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 В следующем примере показана функция Java, которая создает сообщение очереди при срабатывании запросом HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@QueueOutput` для параметров, значения которых будут записываться в хранилище очередей.  Тип параметра `OutputBinding<T>`должен `T` быть, где находится какой-либо родной тип Java POJO.

---

## <a name="attributes-and-annotations"></a>Атрибуты и аннотации

# <a name="c"></a>[C #](#tab/csharp)

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

Этот атрибут применяется к параметру `out` или возвращаемому значению функции. Конструктор атрибута принимает имя очереди, как показано в следующем примере:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Для полного примера [см.](#example)

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Дополнительные сведения см. в разделе "Атрибуты триггера".

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Атрибуты не поддерживаются скриптом C'.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Атрибуты не поддерживаются JavaScript.

# <a name="python"></a>[Python](#tab/python)

Атрибуты не поддерживаются Python.

# <a name="java"></a>[Java](#tab/java)

Аннотация `QueueOutput` позволяет написать сообщение как выход функции. В следующем примере показана функция HTTP-триггера, которая создает сообщение очереди.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Свойство    | Описание |
|-------------|-----------------------------|
|`name`       | Объявляет имя параметра в подписи функции. При срабатывании функции значение этого параметра имеет содержимое сообщения очереди. |
|`queueName`  | Объявляет имя очереди в учетной записи хранилища. |
|`connection` | Указывает на строку подключения учетной записи хранилища. |

Параметр, связанный `QueueOutput` с аннотацией, набран как экземпляр [OutputBinding\<T.\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java)

---

## <a name="configuration"></a>Параметр Configuration

В следующей таблице объясняется свойства связывающей конфигурации, `Queue` установленные в файле *function.json* и атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**тип** | Недоступно | Нужно задать значение `queue`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `out`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей очередь в коде функции. Задайте значение `$return`, ссылающееся на возвращаемое значение функции.|
|**очередьИмя** |**ОчередьИмя** | Имя очереди. |
|**Подключения** | **Подключения** |Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если `connection` вы установите "MyStorage", время выполнения функций ищет настройки приложения, которые называются "MyStorage". Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Использование

# <a name="c"></a>[C #](#tab/csharp)

Напишите сообщение одной очереди, используя `out T paramName`такой параметр метода, как . Вы можете использовать этот метод типа возвращаемого значения вместо параметра `out`. `T` может быть любого из следующих типов:

* Объект, сериализуемый как JSON
* `string`
* `byte[]`
* [ОблакоКютюсообщения] 

Если при попытке привязать к `CloudQueueMessage` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

В коде и скрипте C# запишите несколько сообщений очереди с помощью одного из следующих типов: 

* `ICollector<T>` либо `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue).

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Напишите сообщение одной очереди, используя `out T paramName`такой параметр метода, как . Значение, `paramName` указанное в `name` свойстве *function.json*. Вы можете использовать этот метод типа возвращаемого значения вместо параметра `out`. `T` может быть любого из следующих типов:

* Объект, сериализуемый как JSON
* `string`
* `byte[]`
* [ОблакоКютюсообщения] 

Если при попытке привязать к `CloudQueueMessage` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

В коде и скрипте C# запишите несколько сообщений очереди с помощью одного из следующих типов: 

* `ICollector<T>` либо `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue).

# <a name="javascript"></a>[Javascript](#tab/javascript)

Элемент выходной очереди `context.bindings.<NAME>` `<NAME>` доступен через то, где совпадает имя, определенное в *function.json.* Строку или сериализуемый объект JSON можно использовать для полезных данных элемента очереди.

# <a name="python"></a>[Python](#tab/python)

Существует два варианта выхода сообщения концентратора событий из функции:

- **Значение возврата**: `name` Установите свойство `$return`в *function.json* к . При этой конфигурации значение возврата функции сохраняется как сообщение хранения очереди.

- **Императив**: Передайте значение [установленного](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) методу параметра, объявленного как тип [Out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Передаваемые `set` значения сохраняются в качестве сообщения хранения очереди.

# <a name="java"></a>[Java](#tab/java)

Существует два варианта вывода сообщения концентратора событий из функции с помощью аннотации [Очередного выхода:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput)

- **Значение возврата**: Приприменении аннотации к самой функции, значение возврата функции сохраняется как сообщение концентратора событий.

- **Императив**: Чтобы явно установить значение сообщения, примените аннотацию к определенному параметру типа, [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)где `T` находится POJO или любой родной тип Java. В этой конфигурации передача `setValue` значения методу сохраняетзначение в виде сообщения концентратора событий.

---

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка |  Справочник |
|---|---|
| Очередь | [Коды ошибок очередей](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Большой двоичный объект, таблица, очередь | [Коды ошибок хранилища](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Большой двоичный объект, таблица, очередь |  [Устранение неполадок](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Параметры файла host.json

В этом разделе описаны глобальные настройки конфигурации, доступные для этого привязки в версиях 2.x и выше. Приведенный ниже файл «Пример host.json» содержит только настройки версии 2.x для этой привязки. Для получения дополнительной информации о настройках глобальной конфигурации в версиях 2.x и далее см. [ссылку host.json на функции Azure.](functions-host-json.md)

> [!NOTE]
> Чтобы получить дополнительные сведения о файле host.json в Функции 1.x, см. статью [host.json reference for Azure Functions 1.x](functions-host-json-v1.md)(Справочник по файлу host.json для службы "Функции Azure" версии 1.x.).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Свойство  |Значение по умолчанию | Описание |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Максимальный интервал между опросами очереди. Минимум 00:00:00.100 (100 мс) и приращения до 00:01:00 (1 мин.).  В 1.x тип данных составляет миллисекунды, а в 2.x и выше — TimeSpan.|
|visibilityTimeout|00:00:00|Интервал времени между повторными попытками, когда при обработке сообщения возникает сбой. |
|batchSize|16|Количество сообщений очереди, которые среда выполнения функций одновременно получает и обрабатывает в параллельном режиме. Когда число обрабатываемых сообщений достигает `newBatchThreshold`, среда выполнения получает следующий пакет и начинает обработку содержащихся в нем сообщений. Поэтому максимальное количество сообщений, одновременно обрабатываемых каждой функцией, равно `batchSize` плюс `newBatchThreshold`. Это ограничение применяется отдельно к каждой функции, активируемой с помощью очереди. <br><br>Если вы не хотите, чтобы сообщения из одной очереди обрабатывались параллельно, можно установить для `batchSize` значение 1. Тем не менее этот параметр позволяет исключить параллелизм только при условии, что приложение-функция выполняется на одной виртуальной машине. Если приложение-функция развернуто на нескольких виртуальных машинах, каждая машина может запускать один экземпляр каждой функции, активируемой с помощью очереди.<br><br>Максимальное значение `batchSize` — 32. |
|maxDequeueCount|5|Число повторных попыток обработки сообщения, прежде чем поместить его в очередь подозрительных сообщений.|
|newBatchThreshold|batchSize/2|Каждый раз, когда количество сообщений, обрабатываемых параллельно, достигает этого числа, среда выполнения получает другой пакет.|

## <a name="next-steps"></a>Дальнейшие действия

- [Выполнить функцию при изменении данных хранения очереди (Trigger)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[ОблакоКютюсообщения]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
