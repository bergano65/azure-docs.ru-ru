---
title: Привязки служебной шины Azure для службы "Функции Azure"
description: Узнайте, как отправить сообщения служебной шины Azure из функций Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 02d9ce87d45c5f1c9a123aae18f7d710b268f03e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582255"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Выходная привязка служебной шины Azure для функций Azure

Используйте выходную привязку служебной шины Azure для отправки сообщений очереди или раздела.

Дополнительные сведения об установке и сведениях о конфигурации см. в [обзоре](functions-bindings-service-bus-output.md).

## <a name="example"></a>Пример

# <a name="c"></a>[C#](#tab/csharp)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая отправляет сообщение из очереди службы "Служебная шина":

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В следующем примере показаны выходная привязка служебной шины в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция использует триггер таймера для отправки сообщения очереди каждые 15 секунд.

Данные привязки в файле *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Ниже приведен код сценария C#, который создает одно сообщение.

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Ниже приведен код сценария C#, который создает несколько сообщений.

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

В следующем примере показаны выходная привязка служебной шины в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Функция использует триггер таймера для отправки сообщения очереди каждые 15 секунд.

Данные привязки в файле *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Ниже приведен код сценария JavaScript, который создает одно сообщение.

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Ниже приведен код сценария JavaScript, который создает несколько сообщений.

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

В следующем примере показано, как выполнить запись в очередь служебной шины в Python.

Определение привязки служебной шины определяется в *Function. JSON* , где *тип* имеет значение `serviceBus`.

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
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

`set` В * _ \_init_\_. Корректировка*можно записать сообщение в очередь, передав значение методу.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

В следующем примере показана функция Java, которая отправляет сообщение в очередь `myqueue` служебной шины при СРАБАТЫВАНИИ HTTP-запроса.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@QueueOutput` для параметров функции, значение которых будут записываться в очередь Служебной шины Microsoft Azure.  Параметр должен быть типа `OutputBinding<T>`, где T — любой собственный тип Java POJO.

Функции Java также могут записывать в раздел служебной шины. В следующем примере `@ServiceBusTopicOutput` заметка используется для описания конфигурации выходной привязки. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

---

## <a name="attributes-and-annotations"></a>Атрибуты и заметки

# <a name="c"></a>[C#](#tab/csharp)

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Конструктор этого атрибута принимает имя очереди или раздела и подписки. Можно также указать права доступа для подключения. Выбор параметра прав доступа описан в разделе [Привязки служебной шины в Функциях Azure](#configuration). Ниже приведен пример, в котором этот атрибут применяется к возвращаемому значению функции.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Можно задать `Connection` свойство, чтобы указать имя параметра приложения, содержащего строку подключения служебной шины, которая будет использоваться, как показано в следующем примере:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Полный пример см. в разделе [пример выходных данных](#example).

Чтобы указать учетную запись служебной шины для использования на уровне класса, метода или параметра, можно применить атрибут `ServiceBusAccount`.  Дополнительные сведения см. в разделе [Атрибуты триггера для предкомпилированного кода C#](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Атрибуты не поддерживаются скриптом C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются в JavaScript.

# <a name="python"></a>[Python](#tab/python)

Атрибуты не поддерживаются в Python.

# <a name="java"></a>[Java](#tab/java)

Заметки `ServiceBusQueueOutput` и `ServiceBusTopicOutput` доступны для записи сообщения в качестве выходных данных функции. Параметр, снабженный этими заметками, должен быть `OutputBinding<T>` объявлен `T` как, где — тип, соответствующий типу сообщения.

---

## <a name="configuration"></a>Конфигурация

В следующей таблице описаны свойства конфигурации привязки, заданные в файле *Function. JSON* и `ServiceBus` атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | н/д | Для этого свойства нужно задать значение "serviceBus". Это свойство задается автоматически при создании триггера на портале Azure.|
|**двух** | н/д | Для этого свойства необходимо задать значение out. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | н/д | Имя переменной, представляющей сообщение очереди или раздела в коде функции. Задайте значение "$return", ссылающееся на возвращаемое значение функции. |
|**queueName**|**QueueName**|Имя очереди.  Задается только в случае отправки сообщений очереди, а не раздела.
|**topicName**|**топикнаме**|Имя раздела. Задается только в случае отправки сообщений раздела, а не очереди.|
|**connection**;|**Соединен**|Имя параметра приложения, содержащего строку подключения к служебной шине, используемую для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задано значение `connection` "мисервицебус", среда выполнения функций ищет параметр приложения с именем "азуревебжобсмисервицебус". Если оставить строку `connection` пустой, то среда выполнения службы "Функции" будет использовать строку подключения к служебной шине по умолчанию для параметра приложения AzureWebJobsServiceBus.<br><br>Чтобы получить строку подключения, следуйте инструкциям, указанным в разделе [Получение учетных данных управления](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Строка подключения указывается для пространства имен служебной шины, и она не должна ограничиваться определенной очередью или разделом.|
|**accessRights**|**Доступ**|Права доступа для строки подключения. Доступные значения: `manage` и `listen`. Значение по умолчанию — `manage`. Это означает, что у свойства `connection` есть разрешение на **управление**. При использовании строки подключения без разрешения на **управление** задайте для свойства `accessRights` значение "listen". В противном случае выполнение операций, для которых требуются права на управление, в среде выполнения Функций Azure может завершиться ошибкой. В функциях Azure версии 2. x и более поздних это свойство недоступно, поскольку последняя версия пакета SDK служебной шины не поддерживает операции управления.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Использование

В Функциях Azure версии 1.x среда выполнения создает очередь, если ее не существует, и для параметра `accessRights` необходимо установить значение `manage`. В функциях версии 2. x и более поздних, очередь или раздел должны уже существовать. Если указать очередь или раздел, который не существует, функция завершится ошибкой. 

# <a name="c"></a>[C#](#tab/csharp)

Используйте следующие типы параметров для выходной привязки:

* `out T paramName` - `T` может быть любым сериализуемым в JSON типом. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" создает сообщение с пустым объектом.
* `out string`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out byte[]`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out BrokeredMessage`— Если при выходе из функции значение параметра равно null, функции не создают сообщение (для функций 1. x).
* `out Message`— Если при выходе из функции значение параметра равно null, функции не создают сообщение (для функций 2. x и выше).
* `ICollector<T>` или `IAsyncCollector<T>`. Используется для создания нескольких сообщений. Сообщение создается при вызове метода `Add` .

При работе с функциями C#:

* Асинхронным функциям требуется возвращаемое `IAsyncCollector` значение или вместо `out` параметра.

* Чтобы получить доступ к ИДЕНТИФИКАТОРу сеанса, выполните [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) привязку к типу `sessionId` и используйте свойство.

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Используйте следующие типы параметров для выходной привязки:

* `out T paramName` - `T` может быть любым сериализуемым в JSON типом. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" создает сообщение с пустым объектом.
* `out string`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out byte[]`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out BrokeredMessage`— Если при выходе из функции значение параметра равно null, функции не создают сообщение (для функций 1. x).
* `out Message`— Если при выходе из функции значение параметра равно null, функции не создают сообщение (для функций 2. x и выше).
* `ICollector<T>` или `IAsyncCollector<T>`. Используется для создания нескольких сообщений. Сообщение создается при вызове метода `Add` .

При работе с функциями C#:

* Асинхронным функциям требуется возвращаемое `IAsyncCollector` значение или вместо `out` параметра.

* Чтобы получить доступ к ИДЕНТИФИКАТОРу сеанса, выполните [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) привязку к типу `sessionId` и используйте свойство.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Доступ к очереди или разделу с `context.bindings.<name from function.json>`помощью. В `context.binding.<name>`можно назначить строку, массив байтов или объект JavaScript (десериализовать в JSON).

# <a name="python"></a>[Python](#tab/python)

Используйте [пакет SDK служебной шины Azure](https://docs.microsoft.com/azure/service-bus-messaging) , а не встроенную выходную привязку.

# <a name="java"></a>[Java](#tab/java)

Используйте [пакет SDK служебной шины Azure](https://docs.microsoft.com/azure/service-bus-messaging) , а не встроенную выходную привязку.

---

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочник |
|---|---|
| Служебная шина | [Коды ошибок службы "Служебная шина"](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Служебная шина | [Ограничения службы "Служебная шина"](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Параметры файла host.json

В этом разделе описаны глобальные параметры конфигурации, доступные для этой привязки в версиях 2. x и более поздних. Пример файла host. JSON ниже содержит только параметры этой привязки. Дополнительные сведения о глобальных параметрах конфигурации см. в [справочнике по Host. JSON для функций Azure версии](functions-host-json.md).

> [!NOTE]
> Чтобы получить дополнительные сведения о файле host.json в Функции 1.x, см. статью [host.json reference for Azure Functions 1.x](functions-host-json-v1.md)(Справочник по файлу host.json для службы "Функции Azure" версии 1.x.).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|Свойство  |По умолчанию | Описание |
|---------|---------|---------|
|prefetchCount|0|Возвращает или задает количество сообщений, которые получатель сообщения может одновременно запрашивать.|
|maxAutoRenewDuration|00:05:00|Максимальный период времени, в течение которого блокировка сообщения будет продлеваться автоматически.|
|autoComplete|Да|Должен ли триггер немедленно помечать сообщение как завершенное (автозаполнение) или ожидать успешного завершения работы функции для завершения вызова.|
|maxConcurrentCalls|16|Максимальное число параллельных вызовов к обратному вызову, которое должен инициировать процесс обработки сообщений. По умолчанию в среде выполнения службы "Функции" одновременно обрабатывается несколько сообщений очереди. Чтобы среда выполнения обрабатывала в любой момент времени только одно сообщение очереди или раздела, для свойства `maxConcurrentCalls` нужно задать значение 1. |

## <a name="next-steps"></a>Дальнейшие шаги

- [Выполнение функции при создании сообщения очереди или раздела служебной шины (триггер)](./functions-bindings-service-bus-trigger.md)
