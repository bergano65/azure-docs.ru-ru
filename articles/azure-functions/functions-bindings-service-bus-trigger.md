---
title: Привязки служебной шины Azure для службы "Функции Azure"
description: Научитесь запускать функцию Azure при создании сообщений Azure Service Bus.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273563"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Триггер службы Azure для функций Azure

Используйте триггер служебной шины для ответа на сообщения из очереди или раздела служебной шины.

Для получения информации о настройке и деталях конфигурации, [см.](functions-bindings-service-bus-output.md)

## <a name="example"></a>Пример

# <a name="c"></a>[C #](#tab/csharp)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая считывает [метаданные сообщения](#message-metadata) и заносит в журнал сообщение из очереди службы "Служебная шина".

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

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В следующем примере показаны привязка триггера служебной шины в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция считывает [метаданные сообщения](#message-metadata) и заносит в журнал сообщение из очереди службы "Служебная шина".

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

В следующем примере показаны привязка триггера служебной шины в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Функция считывает [метаданные сообщения](#message-metadata) и заносит в журнал сообщение из очереди службы "Служебная шина". 

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

# <a name="python"></a>[Python](#tab/python)

В следующем примере показано, как читать сообщение очереди в автобусе службы с помощью триггера.

Привязка шин обслуживания определяется в *function.json,* где *тип* установлен. `serviceBusTrigger`

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

Код в * _ \_init_\_.py* объявляет `func.ServiceBusMessage`параметр как, который позволяет читать сообщение очереди в вашей функции.

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

# <a name="java"></a>[Java](#tab/java)

Следующая функция Java `@ServiceBusQueueTrigger` использует аннотацию из [библиотеки выполнения функций Java](/java/api/overview/azure/functions/runtime) для описания конфигурации триггера очереди Service Bus. Функция захватывает сообщение, помещенное в очередь, и добавляет его в журналы.

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

Функции Java также могут быть запущены при добавлении сообщения в тему «Автобус обслуживания». В следующем примере `@ServiceBusTopicTrigger` используется аннотация для описания конфигурации триггера.

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

---

## <a name="attributes-and-annotations"></a>Атрибуты и аннотации

# <a name="c"></a>[C #](#tab/csharp)

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки триггера службы "Служебная шина":

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Конструктор этого атрибута принимает имя очереди или раздела и подписки. В Функциях Azure версии 1.х можно также указать права доступа для подключения. Если права доступа не указаны, то используется значение по умолчанию, `Manage`. Дополнительные сведения см. в разделе [Конфигурация триггера](#configuration).

  Ниже приведен пример, в котором показано, как атрибут используется со строковым параметром.

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Можно указать `Connection` свойство для указания имени настройки приложения, содержащей строку подключения Service Bus для использования, как показано в следующем примере:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Для полного примера [см. Триггер - например.](#example)

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

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Атрибуты не поддерживаются скриптом C'.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Атрибуты не поддерживаются JavaScript.

# <a name="python"></a>[Python](#tab/python)

Атрибуты не поддерживаются Python.

# <a name="java"></a>[Java](#tab/java)

Аннотация `ServiceBusQueueTrigger` позволяет создать функцию, которая работает при создании сообщения очереди в очередь Service Bus. Доступны ею параметры конфигурации: имя очереди и имя строки соединения.

Аннотация `ServiceBusTopicTrigger` позволяет назначить тему и подписку для таргетинга на то, какие данные запускают функцию.

Более подробно можно посмотреть [пример](#example) триггера.

---

## <a name="configuration"></a>Параметр Configuration

В следующей таблице объясняется свойства связывающей конфигурации, `ServiceBusTrigger` установленные в файле *function.json* и атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**тип** | Недоступно | Для этого свойства нужно задать значение "serviceBusTrigger". Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Для этого свойства необходимо задать значение "in". Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей сообщение очереди или раздела в коде функции. |
|**очередьИмя**|**ОчередьИмя**|Имя очереди для отслеживания.  Задается только в том случае, если отслеживается очередь, но не раздел.
|**topicName**|**Тема**|Имя отслеживаемого раздела. Задается только в том случае, если отслеживается раздел, но не очередь.|
|**подпискаИмя**|**ПодпискаИмя**|Имя отслеживаемой подписки. Задается только в том случае, если отслеживается раздел, но не очередь.|
|**Подключения**|**Подключения**|Имя параметра приложения, содержащего строку подключения к служебной шине, используемую для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если `connection` вы установите "MyServiceBus", время выполнения функций ищет настройки приложения, которые называются "AzureWebWebJobsMyServiceBus". Если оставить строку `connection` пустой, то среда выполнения службы "Функции" будет использовать строку подключения к служебной шине по умолчанию для параметра приложения AzureWebJobsServiceBus.<br><br>Чтобы получить строку подключения, следуйте инструкциям, указанным в разделе [Получение учетных данных управления](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Строка подключения указывается для пространства имен служебной шины, и она не должна ограничиваться определенной очередью или разделом. |
|**accessRights**|**Доступ**|Права доступа для строки подключения. Доступные значения: `manage` и `listen`. Значение по умолчанию — `manage`. Это означает, что у свойства `connection` есть разрешение на **управление**. При использовании строки подключения без разрешения на **управление** задайте для свойства `accessRights` значение "listen". В противном случае выполнение операций, для которых требуются права на управление, в среде выполнения Функций Azure может завершиться ошибкой. В версии Azure Functions 2.x и выше это свойство недоступно, поскольку последняя версия SDK Service Bus не поддерживает управление операциями.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`при подключении к очереди или [подписке, осведомленной о сеансах.](../service-bus-messaging/message-sessions.md) `false`в противном случае значение по умолчанию является значением по умолчанию.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Использование

# <a name="c"></a>[C #](#tab/csharp)

Для сообщения очереди или темы доступны следующие типы параметров:

* `string`. Если сообщение является текстом.
* `byte[]`. Используется для двоичных данных.
* Пользовательский тип. Если сообщение содержит JSON, то служба "Функции Azure" пытается выполнить десериализацию данных JSON.
* `BrokeredMessage`- Дает вам десериализованное сообщение с методом [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Эти типы параметров относятся к версии Azure Functions 1.x; для 2.x и [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) выше, `BrokeredMessage`используйте вместо .

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Для сообщения очереди или темы доступны следующие типы параметров:

* `string`. Если сообщение является текстом.
* `byte[]`. Используется для двоичных данных.
* Пользовательский тип. Если сообщение содержит JSON, то служба "Функции Azure" пытается выполнить десериализацию данных JSON.
* `BrokeredMessage`- Дает вам десериализованное сообщение с методом [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Эти параметры относятся к версии Azure Functions 1.x; для 2.x и [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) выше, `BrokeredMessage`используйте вместо .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Доступ к очереди или `context.bindings.<name from function.json>`сообщению темы с помощью . Сообщение служебной шины передается в функцию в качестве строки или объекта JSON.

# <a name="python"></a>[Python](#tab/python)

Сообщение очереди доступно функции через параметр, набранный как. `func.ServiceBusMessage` Сообщение служебной шины передается в функцию в качестве строки или объекта JSON.

# <a name="java"></a>[Java](#tab/java)

Входящие сообщения Автобуса обслуживания доступны `ServiceBusQueueMessage` `ServiceBusTopicMessage` через или параметр.

[Смотрите пример для получения подробной информации](#example).

---

## <a name="poison-messages"></a>Ядовитые сообщения

В службе "Функции Azure" невозможно управление обработкой сообщений о сбое или настройка этой обработки. Служебная шина сама обрабатывает сообщения о сбое.

## <a name="peeklock-behavior"></a> Поведение PeekLock

Среда выполнения службы "Функции" получает сообщение в [режиме PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Она вызывает `Complete` для сообщения, если функция выполнена успешно, или `Abandon` в случае сбоя. Если функция выполняется дольше времени ожидания `PeekLock`, блокировка возобновляется автоматически до тех пор, пока выполняется функция. 

Параметр `maxAutoRenewDuration` можно задать в файле *host.json*. Этот параметр сопоставляется с параметром [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). В соответствии с документацией по служебной шине максимально допустимое значение для этого параметра равно 5 минутам, тогда как ограничение времени выполнения функций можно увеличить с 5 минут по умолчанию до 10 минут. Этого не стоит делать для функций служебной шины, потому что лимит продления служебной шины будет превышен.

## <a name="message-metadata"></a>Метаданные сообщений

Триггер служебной шины предоставляет несколько [свойств метаданных](./functions-bindings-expressions-patterns.md#trigger-metadata). Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти свойства являются членами класса [BrokeredMessage.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)

|Свойство|Тип|Описание|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Число доставок.|
|`DeadLetterSource`|`string`|Источник недоставленных сообщений.|
|`ExpiresAtUtc`|`DateTime`|Время окончания срока действия в формате UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Время попадания в очередь в формате UTC.|
|`MessageId`|`string`|Определяемое пользователем значение, используемое служебной шиной для выявления повторяющихся сообщений.|
|`ContentType`|`string`|Идентификатор типа содержимого, используемый отправителем и получателем для логики, конкретной приложения.|
|`ReplyTo`|`string`|Адрес очереди для ответа.|
|`SequenceNumber`|`Int64`|Уникальный номер, назначенный сообщению службой "Служебная шина".|
|`To`|`string`|Адрес для отправки.|
|`Label`|`string`|Метка, специфичная для приложения.|
|`CorrelationId`|`string`|Идентификатор корреляции.|

См. [примеры кода](#example), в которых используются эти свойства, в предыдущих разделах этой статьи.

## <a name="next-steps"></a>Дальнейшие действия

- [Отправка сообщений шины Azure от функций Azure (связывание вывода)](./functions-bindings-service-bus-output.md)
