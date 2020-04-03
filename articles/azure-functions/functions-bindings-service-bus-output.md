---
title: Привязки служебной шины Azure для службы "Функции Azure"
description: Научитесь отправлять сообщения Azure Service Bus из функций Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 02d9ce87d45c5f1c9a123aae18f7d710b268f03e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582255"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Привязка выходного сообщения шины Azure для функций Azure

Используйте выходную привязку служебной шины Azure для отправки сообщений очереди или раздела.

Для получения информации о настройке и деталях конфигурации, [см.](functions-bindings-service-bus-output.md)

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

В следующем примере показано, как выписать очередь в автобусе службы в Python.

Определение связывания service Bus определяется в *function.json,* где *тип* установлен. `serviceBus`

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

В `set` init *\_.py вы можете выписать сообщение в очередь, передав значение методу. _ \__*

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

В следующем примере показана функция Java, которая `myqueue` отправляет сообщение в очередь «Автобус обслуживания» при срабатывании запросом HTTP.

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

Функции Java также могут записываться на тему «Автобус обслуживания». В следующем примере `@ServiceBusTopicOutput` используется аннотация для описания конфигурации для связывания вывода. 

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

## <a name="attributes-and-annotations"></a>Атрибуты и аннотации

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

Можно указать `Connection` свойство для указания имени настройки приложения, содержащей строку подключения Service Bus для использования, как показано в следующем примере:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Для полного примера [см.](#example)

Чтобы указать учетную запись служебной шины для использования на уровне класса, метода или параметра, можно применить атрибут `ServiceBusAccount`.  Дополнительные сведения см. в разделе [Атрибуты триггера для предкомпилированного кода C#](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Атрибуты не поддерживаются скриптом C'.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются JavaScript.

# <a name="python"></a>[Python](#tab/python)

Атрибуты не поддерживаются Python.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusTopicOutput` Для `ServiceBusQueueOutput` записи сообщения в качестве вывода функций доступны и аннотации. Параметр, украшенный этими аннотациями, должен быть объявлен `OutputBinding<T>` как `T` тип, соответствующий типу сообщения.

---

## <a name="configuration"></a>Параметр Configuration

В следующей таблице объясняется свойства связывающей конфигурации, `ServiceBus` установленные в файле *function.json* и атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**тип** | Недоступно | Для этого свойства нужно задать значение "serviceBus". Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Для этого свойства необходимо задать значение out. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей сообщение очереди или раздела в коде функции. Задайте значение "$return", ссылающееся на возвращаемое значение функции. |
|**очередьИмя**|**ОчередьИмя**|Имя очереди.  Задается только в случае отправки сообщений очереди, а не раздела.
|**topicName**|**Тема**|Имя раздела. Задается только в случае отправки сообщений раздела, а не очереди.|
|**Подключения**|**Подключения**|Имя параметра приложения, содержащего строку подключения к служебной шине, используемую для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если `connection` вы установите "MyServiceBus", время выполнения функций ищет настройки приложения, которые называются "AzureWebWebJobsMyServiceBus". Если оставить строку `connection` пустой, то среда выполнения службы "Функции" будет использовать строку подключения к служебной шине по умолчанию для параметра приложения AzureWebJobsServiceBus.<br><br>Чтобы получить строку подключения, следуйте инструкциям, указанным в разделе [Получение учетных данных управления](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Строка подключения указывается для пространства имен служебной шины, и она не должна ограничиваться определенной очередью или разделом.|
|**accessRights**|**Доступ**|Права доступа для строки подключения. Доступные значения: `manage` и `listen`. Значение по умолчанию — `manage`. Это означает, что у свойства `connection` есть разрешение на **управление**. При использовании строки подключения без разрешения на **управление** задайте для свойства `accessRights` значение "listen". В противном случае выполнение операций, для которых требуются права на управление, в среде выполнения Функций Azure может завершиться ошибкой. В версии Azure Functions 2.x и выше это свойство недоступно, поскольку последняя версия SDK Service Bus не поддерживает управление операциями.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Использование

В Функциях Azure версии 1.x среда выполнения создает очередь, если ее не существует, и для параметра `accessRights` необходимо установить значение `manage`. В версии Функции 2.x и выше очередь или тема должна уже существовать; если вы укажете очередь или тему, которая не существует, функция выйдет из строя. 

# <a name="c"></a>[C#](#tab/csharp)

Используйте следующие типы параметров для выходного связывания:

* `out T paramName` - `T` может быть любым сериализуемым в JSON типом. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" создает сообщение с пустым объектом.
* `out string`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out byte[]`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out BrokeredMessage`- Если значение параметра является нулевым при выходе функции, функции не создают сообщение (для функций 1.x)
* `out Message`- Если значение параметра является нулевым при выходе функции, функции не создают сообщение (для функций 2.x и выше)
* `ICollector<T>` или `IAsyncCollector<T>`. Используется для создания нескольких сообщений. Сообщение создается при вызове метода `Add` .

При работе с функциями C':

* Функции Async нуждаются `IAsyncCollector` в значении возврата или вместо `out` параметра.

* Чтобы получить доступ к идентификатору сеанса, свяжитесь с типом [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) и используйте свойство. `sessionId`

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Используйте следующие типы параметров для выходного связывания:

* `out T paramName` - `T` может быть любым сериализуемым в JSON типом. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" создает сообщение с пустым объектом.
* `out string`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out byte[]`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out BrokeredMessage`- Если значение параметра является нулевым при выходе функции, функции не создают сообщение (для функций 1.x)
* `out Message`- Если значение параметра является нулевым при выходе функции, функции не создают сообщение (для функций 2.x и выше)
* `ICollector<T>` или `IAsyncCollector<T>`. Используется для создания нескольких сообщений. Сообщение создается при вызове метода `Add` .

При работе с функциями C':

* Функции Async нуждаются `IAsyncCollector` в значении возврата или вместо `out` параметра.

* Чтобы получить доступ к идентификатору сеанса, свяжитесь с типом [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) и используйте свойство. `sessionId`

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Доступ к очереди `context.bindings.<name from function.json>`или теме с помощью . Можно назначить строку, массив байт или объект JavaScript (десериализованный `context.binding.<name>`в JSON) для .

# <a name="python"></a>[Python](#tab/python)

Используйте [SDK шины Azure,](https://docs.microsoft.com/azure/service-bus-messaging) а не встроенную связку вывода.

# <a name="java"></a>[Java](#tab/java)

Используйте [SDK шины Azure,](https://docs.microsoft.com/azure/service-bus-messaging) а не встроенную связку вывода.

---

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочник |
|---|---|
| Служебная шина | [Коды ошибок службы "Служебная шина"](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Служебная шина | [Ограничения службы "Служебная шина"](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Параметры файла host.json

В этом разделе описаны глобальные настройки конфигурации, доступные для этого привязки в версиях 2.x и выше. Приведенный ниже файл примера host.json содержит только параметры для этой привязки. Для получения дополнительной информации о глобальных настройках конфигурации см. [host.json для версии Azure Functions.](functions-host-json.md)

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

|Свойство.  |По умолчанию | Описание |
|---------|---------|---------|
|prefetchCount|0|Получает или устанавливает количество сообщений, которые получатель сообщения может одновременно запросить.|
|maxAutoRenewDuration|00:05:00|Максимальный период времени, в течение которого блокировка сообщения будет продлеваться автоматически.|
|autoComplete|Да|Должен ли триггер немедленно пометить сообщение как полное (автозаполненное) или ждать успешного выхода функции для завершения.|
|maxConcurrentCalls|16|Максимальное число параллельных вызовов к обратному вызову, которое должен инициировать процесс обработки сообщений. По умолчанию в среде выполнения службы "Функции" одновременно обрабатывается несколько сообщений очереди. Чтобы среда выполнения обрабатывала в любой момент времени только одно сообщение очереди или раздела, для свойства `maxConcurrentCalls` нужно задать значение 1. |

## <a name="next-steps"></a>Следующие шаги

- [Выполнить функцию при создании очереди в автобусе службы или сообщении темы (Trigger)](./functions-bindings-service-bus-trigger.md)
