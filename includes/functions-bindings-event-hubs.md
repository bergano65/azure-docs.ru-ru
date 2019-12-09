---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 27333f272ca5000fd3b09b305712875c065f6bc7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924438"
---
## <a name="trigger"></a>Триггер

Используйте триггер функции для реагирования на событие, отправленное в поток событий концентратора событий. Для настройки триггера необходимо иметь доступ на чтение к базовому концентратору событий. При запуске функции сообщение, передаваемое в функцию, вводится в виде строки.

## <a name="trigger---scaling"></a>Триггер: масштабирование

Каждый экземпляр функции, активируемой событием, поддерживается одним экземпляром [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) . Триггер (на основе концентраторов событий) гарантирует, что только один экземпляр [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) может получить аренду для данного раздела.

Например, рассмотрим концентратор событий:

* 10 секций
* 1 000 события равномерно распределены по всем секциям с 100 сообщениями в каждой секции

При первом включении у функции будет только 1 экземпляр. Давайте назовем первый экземпляр функции `Function_0`. Функция `Function_0` имеет один экземпляр [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) , который содержит аренду для всех десяти секций. Этот экземпляр начнет считывать события из разделов 0–9. При этом возможно следующее:

* **Новые экземпляры функций не требуются**: `Function_0` может обрабатывать все события 1 000 до вступления в силу логики масштабирования функций. В этом случае все сообщения 1 000 обрабатываются `Function_0`.

* **Добавлен дополнительный экземпляр функции**: Если логика масштабирования функций определяет, что `Function_0` содержит больше сообщений, чем может обработать, создается новый экземпляр приложения-функции (`Function_1`). Эта новая функция также имеет связанный экземпляр [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Так как базовые концентраторы событий обнаруживают, что новый экземпляр узла пытается читать сообщения, он распределяет секции по экземплярам узлов. Например, разделы 0–4 могут быть назначены `Function_0`, а разделы 5–9 — `Function_1`.

* **Добавлено больше экземпляров функций**: Если логика масштабирования функций определяет, что `Function_0` и `Function_1` имеют больше сообщений, чем они могут обработать, создаются новые экземпляры приложения `Functions_N` функции.  Приложения создаются в точке, где `N` больше числа разделов концентратора событий. В нашем примере Центры событий снова распределяют нагрузку разделов, в этом случае — по экземплярам `Function_0`...`Functions_9`.

Когда функции масштабируются, `N` экземпляры являются числом, превышающим число разделов концентратора событий. Это делается для того, чтобы обеспечить доступность экземпляров [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) для получения блокировок на секциях по мере их появления в других экземплярах. Вы платите только за ресурсы, используемые при выполнении экземпляра функции. Иными словами, у вас нет оплаты за эту избыточную подготовку.

Когда функция выполнена (с ошибками или без), в связанную учетную запись хранения добавляются контрольные точки. После завершения проверки все сообщения 1 000 никогда не извлекаются снова.

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Сценарий C# (CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показана [функция C#](../articles/azure-functions/functions-dotnet-class-library.md), которая записывает в журнал текст сообщений триггера концентратора событий.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Чтобы получить доступ к [метаданным события](#trigger---event-metadata) в коде функции, создайте привязку к объекту [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (требуется инструкция using для `Microsoft.Azure.EventHubs`). Вы также можете получить доступ к тем же свойствам, используя выражения привязки в сигнатуре метода.  В следующем примере показаны оба способа получения одних и тех же данных:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Для пакетного получения событий сделайте `string` или `EventData` массивом.  

> [!NOTE]
> При пакетном получении нельзя привязываться к параметрам метода (как в примере выше) с помощью `DateTime enqueuedTimeUtc`. Параметры необходимо получать из каждого объекта `EventData`.  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция сценария C#](../articles/azure-functions/functions-reference-csharp.md), которая использует эту привязку. Эта функция записывает в журнал текст сообщений триггера концентратора событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*.

#### <a name="version-2x-and-higher"></a>Версии 2. x и выше

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Версия 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ниже приведен код скрипта C#.

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Чтобы получить доступ к [метаданным события](#trigger---event-metadata) в коде функции, создайте привязку к объекту [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (требуется инструкция using для `Microsoft.Azure.EventHubs`). Вы также можете получить доступ к тем же свойствам, используя выражения привязки в сигнатуре метода.  В следующем примере показаны оба способа получения одних и тех же данных:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Для пакетного получения событий создайте массив `string` или `EventData`.

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Пример F# в триггере

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция F#](../articles/azure-functions/functions-reference-fsharp.md), которая использует эту привязку. Эта функция записывает в журнал текст сообщений триггера концентратора событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. 

#### <a name="version-2x-and-higher"></a>Версии 2. x и выше

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Версия 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ниже показан код F#.

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Пример JavaScript в триггере

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция JavaScript](../articles/azure-functions/functions-reference-node.md), которая использует эту привязку. Функция считывает [метаданные события](#trigger---event-metadata) и записывает сообщение в журнал.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*.

#### <a name="version-2x-and-higher"></a>Версии 2. x и выше

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Версия 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Чтобы получить события в пакете, задайте для параметра `cardinality` в файле *function.json* значение `many`, как показано в приведенных ниже примерах.

#### <a name="version-2x-and-higher"></a>Версии 2. x и выше

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Версия 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---python-example"></a>Пример Python: триггер

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция Python](../articles/azure-functions/functions-reference-python.md), которая использует эту привязку. Функция считывает [метаданные события](#trigger---event-metadata) и записывает сообщение в журнал.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ниже приведен код Python.

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Пример Java в триггере

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция Java](../articles/azure-functions/functions-reference-java.md), которая использует эту привязку. Эта функция записывает в журнал текст сообщений триггера концентратора событий.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `EventHubTrigger` для параметров, значения которых будут поступать из концентратора событий. Параметры с этими заметками запускают функцию, когда происходит событие.  Эту аннотацию можно использовать с собственными типами Java, POJO или значениями, допускающими значение null, с помощью необязательного\<T >.

## <a name="trigger---attributes"></a>Атрибуты триггера

В [библиотеках классов C#](../articles/azure-functions/functions-dotnet-class-library.md) используйте атрибут [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs).

Конструктор атрибута принимает имя концентратора событий, имя группы потребителей и имя параметра приложения, содержащего строку подключения. Дополнительные сведения об этих параметрах см. в разделе [Привязки концентраторов событий функций Azure](#trigger---configuration). Ниже приведен пример атрибута `EventHubTriggerAttribute`.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Полный пример см. в разделе [Пример C# в триггере](#trigger---c-example).

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `EventHubTrigger`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Н/Д | Нужно задать значение `eventHubTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Н/Д | Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Н/Д | Имя переменной, представляющей элемент события в коде функции. |
|**path** |**EventHubName** | Только служба "Функции" версии 1.x. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. |
|**eventHubName** |**EventHubName** | Функции 2. x и более поздних версий. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. |
|**consumerGroup** |**ConsumerGroup** | Необязательное свойство, которое используется для задания [группы потребителей](../articles/event-hubs/event-hubs-features.md#event-consumers), используемой для подписки на события в концентраторе. Если аргумент опущен, используется группа потребителей `$Default`. |
|**кратность** | Н/Д | Для JavaScript. Задайте значение `many`, чтобы включить пакетную обработку.  Если опустить это значение или задать `one`, функции будет передано одно сообщение. |
|**подключение** |**Connection** | Имя параметра приложения, содержащего строку подключения к пространству имен концентратора событий. Скопируйте эту строку подключения, нажав кнопку **Сведения о подключении** для [пространства имен](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), а не сам концентратор событий. Для активации триггера эта строка подключения должна обладать, по крайней мере, правами на чтение.|
|**path**|**EventHubName**|Имя концентратора событий. Ссылаться на него можно с помощью параметров приложения `%eventHubName%`.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Триггер — метаданные события

Триггер Центров событий предоставляет несколько [свойств метаданных](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти свойства относятся к классу [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata).

|Свойство|Тип|Описание|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Экземпляр `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Время попадания в очередь в формате UTC.|
|`Offset`|`string`|Смещение данных относительно потока разделов концентратора событий. Смещение — это маркер или идентификатор события в потоке Центров событий. Этот идентификатор уникален внутри раздела потока Центров событий.|
|`PartitionKey`|`string`|Раздел, в который следует отправлять данные события.|
|`Properties`|`IDictionary<String,Object>`|Свойства пользователя данных события.|
|`SequenceNumber`|`Int64`|Регистрационный номер транзакции события в журнале.|
|`SystemProperties`|`IDictionary<String,Object>`|Свойства системы, включая данные события.|

См. [примеры кода](#trigger---example), в которых используются эти свойства, в предыдущих разделах этой статьи.

## <a name="trigger---hostjson-properties"></a>Свойства host.json в триггере

В файле [host.json](../articles/azure-functions/functions-host-json.md#eventhub) содержатся параметры, управляющие реакцией триггера Центров событий на событие.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Выходные данные

Используйте выходную привязку Центров событий для записи событий в поток событий. Чтобы записывать события в концентратор событий, необходимо иметь разрешение на оправку в него событий.

Прежде чем пытаться реализовать привязку выходные данные, убедитесь, что имеются необходимые ссылки на пакет.

## <a name="output---example"></a>Пример выходных данных

Языковой пример см. в разделах:

* [C#](#output---c-example)
* [Сценарий C# (CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Пример выходных данных C#

В следующем примере показана [функция C#](../articles/azure-functions/functions-dotnet-class-library.md), которая записывает сообщение в концентратор событий, используя возвращаемое значение метода в качестве выходных данных.

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

В следующем примере показано, как использовать интерфейс `IAsyncCollector` для отправки пакета сообщений. Этот сценарий часто используется при обработке сообщений, поступающих из одного концентратора событий, и при отправке результата в другой концентратор событий.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

### <a name="output---c-script-example"></a>Пример выходных данных скрипта C#

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция сценария C#](../articles/azure-functions/functions-reference-csharp.md), которая использует эту привязку. Эта функция записывает сообщение в концентратор событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. Первый пример — для функций 2. x и более поздних, а второй — для функций 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Ниже приведен код сценария C#, который создает одно сообщение.

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Ниже приведен код сценария C#, который создает несколько сообщений.

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Пример выходных данных F#

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция F#](../articles/azure-functions/functions-reference-fsharp.md), которая использует эту привязку. Эта функция записывает сообщение в концентратор событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. Первый пример — для функций 2. x и более поздних, а второй — для функций 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Ниже показан код F#.

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Пример выходных данных JavaScript

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция JavaScript](../articles/azure-functions/functions-reference-node.md), которая использует эту привязку. Эта функция записывает сообщение в концентратор событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. Первый пример — для функций 2. x и более поздних, а второй — для функций 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Ниже приведен код JavaScript, который отправляет отдельное сообщение.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Ниже приведен код JavaScript, который отправляет несколько сообщений.

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Пример Python: выходные данные

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция Python](../articles/azure-functions/functions-reference-python.md), которая использует эту привязку. Эта функция записывает сообщение в концентратор событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Ниже приведен код Python, который отправляет отдельное сообщение.

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>Пример выходных данных Java

В следующем примере показана функция Java, которая записывает в концентратор событий сообщение, содержащее текущее время.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@EventHubOutput` для параметров, значения которых будут опубликованы в концентраторе событий.  Параметр должен быть типа `OutputBinding<T>`, где T — POJO или любой собственный тип Java.

## <a name="output---attributes"></a>Выходные атрибуты

В [библиотеках классов C#](../articles/azure-functions/functions-dotnet-class-library.md) используйте атрибут [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs).

Конструктор атрибута принимает имя концентратора событий и имя параметра приложения, содержащего строку подключения. Дополнительные сведения об этих параметрах см. в разделе [Привязки концентраторов событий функций Azure](#output---configuration). Ниже приведен пример атрибута `EventHub`.

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Полный пример см. в разделе [Пример выходных данных C#](#output---c-example).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `EventHub`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Н/Д | Для этого свойства необходимо задать значение "eventHub" |
|**direction** | Н/Д | Для этого свойства необходимо задать значение out. Этот параметр задается автоматически при создании привязки на портале Azure. |
|**name** | Н/Д | Имя переменной, используемое в коде функции, которая представляет событие. |
|**path** |**EventHubName** | Только служба "Функции" версии 1.x. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. |
|**eventHubName** |**EventHubName** | Функции 2. x и более поздних версий. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. |
|**подключение** |**Connection** | Имя параметра приложения, содержащего строку подключения к пространству имен концентратора событий. Скопируйте эту строку подключения, нажав кнопку **Сведения о подключении** для *пространства имен*, а не сам концентратор событий. Чтобы отправлять сообщения в поток событий, эта строка подключения должна иметь разрешения на отправку.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

В коде C# и сценарии C# для отправки сообщений используйте параметр метода, например `out string paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Для записи нескольких сообщений можно использовать `ICollector<string>` или `IAsyncCollector<string>` вместо `out string`.

В JavaScript для доступа к выходному событию можно использовать `context.bindings.<name>`. `<name>` — это значение, заданное в свойстве `name` файла *function.json*.

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочные материалы |
|---|---|
| Концентратор событий | [Руководство по операциям](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Параметры файла host.json

В этом разделе описаны глобальные параметры конфигурации, доступные для этой привязки в версиях 2. x и более поздних. Пример файла host. JSON ниже содержит только параметры версии 2. x + для этой привязки. Дополнительные сведения о глобальных параметрах конфигурации в версиях 2. x и более поздних версий см. в [справочнике по Host. JSON для функций Azure](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Сведения о файле host.json в Функциях Azure версии 1.x см. в [этой статье](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Свойство  |значение по умолчанию | Описание |
|---------|---------|---------|
|maxBatchSize|64|Максимальное число событий, получаемых в цикле получения.|
|prefetchCount|Н/Д|Значение PrefetchCount по умолчанию, которое будет использоваться базовым узлом EventProcessorHost.|
|batchCheckpointFrequency|1|Количество пакетов событий, которые необходимо обработать перед созданием контрольной точки курсора EventHub.|
