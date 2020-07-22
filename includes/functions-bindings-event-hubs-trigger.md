---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 7826df83506083e2db1bdb011704cb0fef628801
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85378521"
---
Используйте триггер функции для реагирования на событие, отправленное в поток событий концентратора событий. Для настройки триггера необходимо иметь доступ на чтение к базовому концентратору событий. При запуске функции сообщение, передаваемое в функцию, вводится в виде строки.

## <a name="scaling"></a>Масштабирование

Каждый экземпляр функции, активируемой событием, поддерживается одним экземпляром [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) . Триггер (на основе концентраторов событий) гарантирует, что только один экземпляр [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) может получить аренду для данного раздела.

Например, рассмотрим концентратор событий:

* 10 секций
* 1 000 события равномерно распределены по всем секциям с 100 сообщениями в каждой секции

При первом включении у функции будет только 1 экземпляр. Давайте назовем первый экземпляр функции `Function_0` . `Function_0`Функция имеет один экземпляр [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) , который содержит аренду для всех десяти секций. Этот экземпляр начнет считывать события из разделов 0–9. При этом возможно следующее:

* **Новые экземпляры функций не требуются**: `Function_0` может обрабатывать все события 1 000 до вступления в силу логики масштабирования функций. В этом случае все сообщения 1 000 обрабатываются `Function_0` .

* **Добавлен дополнительный экземпляр функции**: Если логика масштабирования функций определяет, что `Function_0` содержит больше сообщений, чем может обработать, создается новый экземпляр приложения-функции ( `Function_1` ). Эта новая функция также имеет связанный экземпляр [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Так как базовые концентраторы событий обнаруживают, что новый экземпляр узла пытается читать сообщения, он распределяет секции между экземплярами узла. Например, разделы 0–4 могут быть назначены `Function_0`, а разделы 5–9 — `Function_1`.

* **Добавлено больше экземпляров функций**: Если логика масштабирования функций определяет, что обе `Function_0` и `Function_1` имеют больше сообщений, чем они могут обработать, `Functions_N` создаются новые экземпляры приложения функции.  Приложения создаются в точке, где больше `N` , чем число разделов в концентраторе событий. В нашем примере Центры событий снова распределяют нагрузку разделов, в этом случае — по экземплярам `Function_0`...`Functions_9`.

Как только происходит масштабирование, `N` число экземпляров превышает число разделов концентратора событий. Этот шаблон используется, чтобы обеспечить доступность экземпляров [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) для получения блокировок на секциях по мере их появления в других экземплярах. Вы платите только за ресурсы, используемые при выполнении экземпляра функции. Иными словами, у вас нет оплаты за эту избыточную подготовку.

Когда функция выполнена (с ошибками или без), в связанную учетную запись хранения добавляются контрольные точки. После завершения проверки все сообщения 1 000 никогда не извлекаются снова.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

В следующем примере показана [функция C#](../articles/azure-functions/functions-dotnet-class-library.md), которая записывает в журнал текст сообщений триггера концентратора событий.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Чтобы получить доступ к [метаданным события](#event-metadata) в коде функции, создайте привязку к объекту [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (требуется инструкция using для `Microsoft.Azure.EventHubs`). Вы также можете получить доступ к тем же свойствам, используя выражения привязки в сигнатуре метода.  В следующем примере показаны оба способа получения одних и тех же данных:

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

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция сценария C#](../articles/azure-functions/functions-reference-csharp.md), которая использует эту привязку. Эта функция записывает в журнал текст сообщений триггера концентратора событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*.

### <a name="version-2x-and-higher"></a>Версия 2.x и более поздние

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Версия 1.x

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

Чтобы получить доступ к [метаданным события](#event-metadata) в коде функции, создайте привязку к объекту [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (требуется инструкция using для `Microsoft.Azure.EventHubs`). Вы также можете получить доступ к тем же свойствам, используя выражения привязки в сигнатуре метода.  В следующем примере показаны оба способа получения одних и тех же данных:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция JavaScript](../articles/azure-functions/functions-reference-node.md), которая использует эту привязку. Функция считывает [метаданные события](#event-metadata) и записывает сообщение в журнал.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*.

### <a name="version-2x-and-higher"></a>Версия 2.x и более поздние

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Версия 1.x

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

### <a name="version-2x-and-higher"></a>Версия 2.x и более поздние

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

### <a name="version-1x"></a>Версия 1.x

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

# <a name="python"></a>[Python](#tab/python)

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция Python](../articles/azure-functions/functions-reference-python.md), которая использует эту привязку. Функция считывает [метаданные события](#event-metadata) и записывает сообщение в журнал.

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

# <a name="java"></a>[Java](#tab/java)

В следующем примере показана привязка триггера концентратора событий, которая регистрирует тело сообщения триггера концентратора событий.

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

 В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `EventHubTrigger` для параметров, значения которых будут поступать из концентратора событий. Параметры с этими заметками запускают функцию, когда происходит событие.  Эта заметка может использоваться с собственными типами Java, объектами POJO или значениями, допускающими значения NULL, используя `Optional<T>`.

 ---

## <a name="attributes-and-annotations"></a>Атрибуты и заметки

# <a name="c"></a>[C#](#tab/csharp)

В [библиотеках классов C#](../articles/azure-functions/functions-dotnet-class-library.md) используйте атрибут [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs).

Конструктор атрибута принимает имя концентратора событий, имя группы потребителей и имя параметра приложения, содержащего строку подключения. Дополнительные сведения об этих параметрах см. в разделе [Привязки концентраторов событий функций Azure](#configuration). Ниже приведен пример атрибута `EventHubTriggerAttribute`.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Полный пример см. в разделе [Пример C# в триггере](#example).

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В скрипте C# атрибуты не поддерживаются.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

В JavaScript атрибуты не поддерживаются.

# <a name="python"></a>[Python](#tab/python)

В Python атрибуты не поддерживаются.

# <a name="java"></a>[Java](#tab/java)

В [библиотеке времени выполнения функций](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)Java Используйте заметку [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) в параметрах, значение которых поступает из концентратора событий. Параметры с этими заметками запускают функцию, когда происходит событие. Эта заметка может использоваться с собственными типами Java, объектами POJO или значениями, допускающими значения NULL, используя `Optional<T>`.

---

## <a name="configuration"></a>Конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `EventHubTrigger`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | н/д | Нужно задать значение `eventHubTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | н/д | Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей элемент события в коде функции. |
|**path** |**EventHubName** | Только служба "Функции" версии 1.x. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. |
|**eventHubName** |**EventHubName** | Функции 2. x и более поздних версий. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. Можно ссылаться через [Параметры приложения](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings)`%eventHubName%` |
|**consumerGroup** |**Группы потребителей** | Необязательное свойство, которое используется для задания [группы потребителей](../articles/event-hubs/event-hubs-features.md#event-consumers), используемой для подписки на события в концентраторе. Если аргумент опущен, используется группа потребителей `$Default`. |
|**количества элементов** | н/д | Используется для всех языков, отличных от C #. Задайте значение `many`, чтобы включить пакетную обработку.  Если параметр опущен или задано значение `one` , в функцию передается одно сообщение.<br><br>В C# это свойство автоматически назначается всякий раз, когда у триггера есть массив для типа.|
|**connection**; |**Соединение** | Имя параметра приложения, содержащего строку подключения к пространству имен концентратора событий. Скопируйте эту строку подключения, нажав кнопку **Сведения о подключении** для [пространства имен](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), а не сам концентратор событий. Для активации триггера эта строка подключения должна обладать, по крайней мере, правами на чтение.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>Метаданные события

Триггер Центров событий предоставляет несколько [свойств метаданных](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Свойства метаданных можно использовать как часть выражений привязки в других привязках или в качестве параметров в коде. Свойства берутся из класса [EVENTDATA](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) .

|Свойство.|Type|Описание:|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Экземпляр `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Время попадания в очередь в формате UTC.|
|`Offset`|`string`|Смещение данных относительно потока разделов концентратора событий. Смещение — это маркер или идентификатор события в потоке Центров событий. Этот идентификатор уникален внутри раздела потока Центров событий.|
|`PartitionKey`|`string`|Раздел, в который следует отправлять данные события.|
|`Properties`|`IDictionary<String,Object>`|Свойства пользователя данных события.|
|`SequenceNumber`|`Int64`|Регистрационный номер транзакции события в журнале.|
|`SystemProperties`|`IDictionary<String,Object>`|Свойства системы, включая данные события.|

См. [примеры кода](#example), в которых используются эти свойства, в предыдущих разделах этой статьи.

## <a name="hostjson-properties"></a>host.jsсвойств
<a name="host-json"></a>

В файле [host.json](../articles/azure-functions/functions-host-json.md#eventhub) содержатся параметры, управляющие реакцией триггера Центров событий на событие. Конфигурация различается в зависимости от версии функций Azure.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]