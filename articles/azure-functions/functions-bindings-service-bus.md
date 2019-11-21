---
title: Привязки служебной шины Azure для службы "Функции Azure"
description: Узнайте, как использовать триггеры и привязки служебной шины Azure в функциях Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: d27058ed0ff3044d98d8428b3065b02e2c24c451
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231048"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Привязки служебной шины Azure для службы "Функции Azure"

В этой статье описывается использование привязок служебной шины Azure в службе "Функции Azure". Функции Azure поддерживают привязки триггера и выходные привязки для очередей и разделов служебной шины.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Привязки служебной шины доступны в пакете NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) версии 2.х. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

Привязки служебной шины доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) версии 3.х. Source code for the package is in the [azure-functions-servicebus-extension](https://github.com/Azure/azure-functions-servicebus-extension) GitHub repository.

> [!NOTE]
> Version 2.x does not create the topic or subscription configured in the `ServiceBusTrigger` instance. Version 2.x is based on [Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) and does not handle queue management.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Триггер

Используйте триггер служебной шины для ответа на сообщения из очереди или раздела служебной шины. 

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Сценарий C# (CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая считывает [метаданные сообщения](#trigger---message-metadata) и заносит в журнал сообщение из очереди службы "Служебная шина".

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показаны привязка триггера служебной шины в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция считывает [метаданные сообщения](#trigger---message-metadata) и заносит в журнал сообщение из очереди службы "Служебная шина".

Данные привязки в файле *function.json*:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Ниже приведен код скрипта C#.

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>Пример F# в триггере

В следующем примере показаны привязка триггера служебной шины в файле *function.json* и [функция F#](functions-reference-fsharp.md), которая использует эту привязку. Эта функция заносит в журнал сообщение очереди служебной шины. 

Данные привязки в файле *function.json*:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Ниже приведен код сценария F#.

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Пример Java в триггере

The following Java function uses the `@ServiceBusQueueTrigger` annotation from the [Java functions runtime library](/java/api/overview/azure/functions/runtime) to describe the configuration for a Service Bus queue trigger. The  function grabs the message placed on the queue and adds it to the logs.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Java functions can also be triggered when a message is added to a Service Bus topic. The following example uses the `@ServiceBusTopicTrigger` annotation to describe the trigger configuration.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

### <a name="trigger---javascript-example"></a>Пример JavaScript в триггере

В следующем примере показаны привязка триггера служебной шины в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Функция считывает [метаданные сообщения](#trigger---message-metadata) и заносит в журнал сообщение из очереди службы "Служебная шина". 

Данные привязки в файле *function.json*:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Ниже показан код сценария JavaScript.

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

### <a name="trigger---python-example"></a>Пример Python: триггер

The following example demonstrates how to read a ServiceBus queue message via a trigger.

A ServiceBus binding is defined in *function.json* where *type* is set to `serviceBusTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

The code in *_\_init_\_.py* declares a parameter as `func.ServiceBusMessage` which allows you to read the queue message in your function.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>Атрибуты триггера

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки триггера службы "Служебная шина":

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Конструктор этого атрибута принимает имя очереди или раздела и подписки. В Функциях Azure версии 1.х можно также указать права доступа для подключения. Если права доступа не указаны, то используется значение по умолчанию, `Manage`. Дополнительные сведения см. в разделе [Конфигурация триггера](#trigger---configuration).

  Ниже приведен пример, в котором показано, как атрибут используется со строковым параметром.

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Чтобы указать учетную запись служебной шины, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Полный пример см. в разделе [Пример C# в триггере](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Предоставляет еще один способ указать используемую учетную запись служебной шины. Конструктор принимает имя параметра приложения, содержащего строку подключения к служебной шине. Атрибут может применяться на уровне класса, метода или параметра. Ниже показан пример уровня класса и метода.

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

Используемая учетная запись служебной шины определяется в следующем порядке:

* Свойство `ServiceBusTrigger` атрибута `Connection`.
* Атрибут `ServiceBusAccount`, примененный к тому же параметру, что и `ServiceBusTrigger`.
* Атрибут `ServiceBusAccount`, примененный к функции.
* Атрибут `ServiceBusAccount`, примененный к классу.
* Параметр приложения AzureWebJobsServiceBus.

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `ServiceBusTrigger`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Н/Д | Для этого свойства нужно задать значение "serviceBusTrigger". Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Н/Д | Для этого свойства необходимо задать значение "in". Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Н/Д | Имя переменной, представляющей сообщение очереди или раздела в коде функции. Задайте значение "$return", ссылающееся на возвращаемое значение функции. |
|**queueName**|**QueueName**|Имя очереди для отслеживания.  Задается только в том случае, если отслеживается очередь, но не раздел.
|**topicName**|**TopicName**|Имя отслеживаемого раздела. Задается только в том случае, если отслеживается раздел, но не очередь.|
|**subscriptionName**|**Параметр SubscriptionName**|Имя отслеживаемой подписки. Задается только в том случае, если отслеживается раздел, но не очередь.|
|**подключение**|**Connection**|Имя параметра приложения, содержащего строку подключения к служебной шине, используемую для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyServiceBus, то среда выполнения службы "Функции" будет искать параметр приложения AzureWebJobsMyServiceBus. Если оставить строку `connection` пустой, то среда выполнения службы "Функции" будет использовать строку подключения к служебной шине по умолчанию для параметра приложения AzureWebJobsServiceBus.<br><br>Чтобы получить строку подключения, следуйте инструкциям, указанным в разделе [Получение учетных данных управления](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Строка подключения указывается для пространства имен служебной шины, и она не должна ограничиваться определенной очередью или разделом. |
|**accessRights**|**Доступ**|Права доступа для строки подключения. Доступные значения: `manage` и `listen`. Значение по умолчанию — `manage`. Это означает, что у свойства `connection` есть разрешение на **управление**. При использовании строки подключения без разрешения на **управление** задайте для свойства `accessRights` значение "listen". В противном случае выполнение операций, для которых требуются права на управление, в среде выполнения Функций Azure может завершиться ошибкой. В Функциях Azure версии 2.x это свойство недоступно, так как последняя версия пакета SDK службы хранилища не поддерживает управление операциями.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Использование триггера

В C# и сценарии C# для сообщения очереди или раздела можно использовать параметры следующих типов:

* `string`. Если сообщение является текстом.
* `byte[]`. Используется для двоичных данных.
* Пользовательский тип. Если сообщение содержит JSON, то служба "Функции Azure" пытается выполнить десериализацию данных JSON.
* `BrokeredMessage` - Gives you the deserialized message with the [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) method.

Эти параметры предназначены для решения "Функции Azure" версии 1.x. Если используется версия 2.x, вместо `BrokeredMessage` примените [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message).

В JavaScript доступ к сообщению очереди или раздела осуществляется с помощью `context.bindings.<name from function.json>`. Сообщение служебной шины передается в функцию в качестве строки или объекта JSON.

## <a name="trigger---poison-messages"></a>Триггеры сообщений о сбое

В службе "Функции Azure" невозможно управление обработкой сообщений о сбое или настройка этой обработки. Служебная шина сама обрабатывает сообщения о сбое.

## <a name="trigger---peeklock-behavior"></a>Поведение PeekLock в триггере

Среда выполнения службы "Функции" получает сообщение в [режиме PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Она вызывает `Complete` для сообщения, если функция выполнена успешно, или `Abandon` в случае сбоя. Если функция выполняется дольше времени ожидания `PeekLock`, блокировка возобновляется автоматически до тех пор, пока выполняется функция. 

Параметр `maxAutoRenewDuration` можно задать в файле *host.json*. Этот параметр сопоставляется с параметром [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). В соответствии с документацией по служебной шине максимально допустимое значение для этого параметра равно 5 минутам, тогда как ограничение времени выполнения функций можно увеличить с 5 минут по умолчанию до 10 минут. Этого не стоит делать для функций служебной шины, потому что лимит продления служебной шины будет превышен.

## <a name="trigger---message-metadata"></a>Метаданные сообщения триггера

Триггер служебной шины предоставляет несколько [свойств метаданных](./functions-bindings-expressions-patterns.md#trigger-metadata). Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти свойства относятся к классу [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).

|Свойство|Тип|Описание|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Число доставок.|
|`DeadLetterSource`|`string`|Источник недоставленных сообщений.|
|`ExpiresAtUtc`|`DateTime`|Время окончания срока действия в формате UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Время попадания в очередь в формате UTC.|
|`MessageId`|`string`|Определяемое пользователем значение, используемое служебной шиной для выявления повторяющихся сообщений.|
|`ContentType`|`string`|Идентификатор типа содержимого, используемый отправителем и получателем для логики конкретного приложения.|
|`ReplyTo`|`string`|Адрес очереди для ответа.|
|`SequenceNumber`|`Int64`|Уникальный номер, назначенный сообщению службой "Служебная шина".|
|`To`|`string`|Адрес для отправки.|
|`Label`|`string`|Метка конкретного приложения.|
|`CorrelationId`|`string`|Идентификатор корреляции.|

> [!NOTE]
> Currently, Service bus trigger that works with session enabled queues and subscriptions is in preview. Please track [this item](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) for any further updates regarding this. 

См. [примеры кода](#trigger---example), в которых используются эти свойства, в предыдущих разделах этой статьи.

## <a name="output"></a>Выходные данные

Используйте выходную привязку служебной шины Azure для отправки сообщений очереди или раздела.

## <a name="output---example"></a>Пример выходных данных

Языковой пример см. в разделах:

* [C#](#output---c-example)
* [Сценарий C# (CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Пример выходных данных C#

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

### <a name="output---c-script-example"></a>Пример выходных данных скрипта C#

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

### <a name="output---f-example"></a>Пример выходных данных F#

В следующем примере показаны выходная привязка служебной шины в файле *function.json* и [функция сценария F#](functions-reference-fsharp.md), которая использует эту привязку. Функция использует триггер таймера для отправки сообщения очереди каждые 15 секунд.

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

Ниже приведен код сценария F#, который создает одно сообщение.

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Пример выходных данных Java

В следующем примере показана функция Java, которая активируется HTTP-запросом и отправляет сообщение в очередь служебной шины `myqueue`.

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

Java functions can also write to a Service Bus topic. The following example uses the `@ServiceBusTopicOutput` annotation to describe the configuration for the output binding. 

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

### <a name="output---javascript-example"></a>Пример выходных данных JavaScript

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

### <a name="output---python-example"></a>Пример Python: выходные данные

The following example demonstrates how to write out to a ServiceBus queue in Python.

A ServiceBue binding definition is defined in *function.json* where *type* is set to `serviceBus`.

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

In *_\_init_\_.py*, you can write out a message to the queue by passing a value to the `set` method.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

## <a name="output---attributes"></a>Выходные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Конструктор этого атрибута принимает имя очереди или раздела и подписки. Можно также указать права доступа для подключения. Выбор параметра прав доступа описан в разделе [Привязки служебной шины в Функциях Azure](#output---configuration). Ниже приведен пример, в котором этот атрибут применяется к возвращаемому значению функции.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Чтобы указать учетную запись служебной шины, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Полный пример см. в разделе [Пример выходных данных C#](#output---c-example).

Чтобы указать учетную запись служебной шины для использования на уровне класса, метода или параметра, можно применить атрибут `ServiceBusAccount`.  Дополнительные сведения см. в разделе [Атрибуты триггера для предкомпилированного кода C#](#trigger---attributes).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `ServiceBus`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Н/Д | Для этого свойства нужно задать значение "serviceBus". Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Н/Д | Для этого свойства необходимо задать значение out. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Н/Д | Имя переменной, представляющей очередь или раздел в коде функции. Задайте значение "$return", ссылающееся на возвращаемое значение функции. |
|**queueName**|**QueueName**|Имя очереди.  Задается только в случае отправки сообщений очереди, а не раздела.
|**topicName**|**TopicName**|Имя отслеживаемого раздела. Задается только в случае отправки сообщений раздела, а не очереди.|
|**подключение**|**Connection**|Имя параметра приложения, содержащего строку подключения к служебной шине, используемую для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyServiceBus, то среда выполнения службы "Функции" будет искать параметр приложения AzureWebJobsMyServiceBus. Если оставить строку `connection` пустой, то среда выполнения службы "Функции" будет использовать строку подключения к служебной шине по умолчанию для параметра приложения AzureWebJobsServiceBus.<br><br>Чтобы получить строку подключения, следуйте инструкциям, указанным в разделе [Получение учетных данных управления](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Строка подключения указывается для пространства имен служебной шины, и она не должна ограничиваться определенной очередью или разделом.|
|**accessRights**|**Доступ**|Права доступа для строки подключения. Доступные значения: `manage` и `listen`. Значение по умолчанию — `manage`. Это означает, что у свойства `connection` есть разрешение на **управление**. При использовании строки подключения без разрешения на **управление** задайте для свойства `accessRights` значение "listen". В противном случае выполнение операций, для которых требуются права на управление, в среде выполнения Функций Azure может завершиться ошибкой. В Функциях Azure версии 2.x это свойство недоступно, так как последняя версия пакета SDK службы хранилища не поддерживает управление операциями.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

В Функциях Azure версии 1.x среда выполнения создает очередь, если ее не существует, и для параметра `accessRights` необходимо установить значение `manage`. В Функциях Azure версии 2.x очередь или раздел должны уже существовать. Если вы укажете несуществующую очередь или раздел, функция завершится с ошибкой. 

В C# и сценарии C# для привязки к выходным данным можно использовать параметры следующих типов:

* `out T paramName` - `T` может быть любым сериализуемым в JSON типом. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" создает сообщение с пустым объектом.
* `out string`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out byte[]`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out BrokeredMessage` - If the parameter value is null when the function exits, Functions does not create a message (for Functions 1.x)
* `out Message` - If the parameter value is null when the function exits, Functions does not create a message (for Functions 2.x)
* `ICollector<T>` или `IAsyncCollector<T>`. Используется для создания нескольких сообщений. Сообщение создается при вызове метода `Add` .

When working with C# functions:

* Async functions need a return value or `IAsyncCollector` instead of an `out` parameter.

* To access the session ID, bind to a [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) type and use the `sessionId` property.

В JavaScript доступ к очереди или разделу осуществляется с помощью `context.bindings.<name from function.json>`. You can assign a string, a byte array, or a JavaScript object (deserialized into JSON) to `context.binding.<name>`.

To send a message to a session-enabled queue in non-C# languages, use the [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) rather than the built-in output binding.

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочные материалы |
|---|---|
| Служебная шина Azure | [Коды ошибок службы "Служебная шина"](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Служебная шина Azure | [Ограничения службы "Служебная шина"](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Параметры файла host.json

В этом разделе описываются глобальные параметры конфигурации, доступные для этой привязки в версии 2.x. В приведенном ниже примере файла host.json содержатся только параметры версии 2.x для этой привязки. Дополнительные сведения о глобальных параметрах конфигурации в версии 2.x см. в статье [Справочник по файлу host.json для Функций Azure](functions-host-json.md).

> [!NOTE]
> Сведения о файле host.json в Функциях Azure версии 1.x см. в [этой статье](functions-host-json-v1.md).

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
            }
        }
    }
}
```

|Свойство  |значение по умолчанию | Описание |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|Максимальный период времени, в течение которого блокировка сообщения будет продлеваться автоматически.|
|autoComplete|true|Триггер следует отметить как выполненный (автозавершение) или дождаться обработки вызова завершения.|
|maxConcurrentCalls|16|Максимальное число параллельных вызовов к обратному вызову, которое должен инициировать процесс обработки сообщений. По умолчанию в среде выполнения службы "Функции" одновременно обрабатывается несколько сообщений очереди. Чтобы среда выполнения обрабатывала в любой момент времени только одно сообщение очереди или раздела, для свойства `maxConcurrentCalls` нужно задать значение 1. |
|prefetchCount|Н/Д|Значение PrefetchCount по умолчанию, которое будет использоваться базовым компонентом MessageReceiver.|


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
