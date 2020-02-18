---
title: Выходная привязка сетки событий Azure для функций Azure
description: Узнайте, как отправить событие службы "Сетка событий" в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368952"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Выходная привязка сетки событий Azure для функций Azure

Используйте выходную привязку сетки событий для записи событий в пользовательский раздел. Необходимо иметь допустимый [ключ доступа для пользовательского раздела](../event-grid/security-authentication.md#custom-topic-publishing).

Дополнительные сведения об установке и сведениях о конфигурации см. в [обзоре](./functions-bindings-event-grid.md).

> [!NOTE]
> Выходная привязка сетки событий не поддерживает подписанные URL-адрес (токены SAS). Необходимо использовать ключ доступа раздела.

> [!IMPORTANT]
> Выходная привязка сетки событий доступна только для функций 2. x и более поздних версий.

## <a name="example"></a>Пример

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

В следующем примере показана [ C# функция](functions-dotnet-class-library.md) , которая записывает сообщение в пользовательский раздел сетки событий, используя возвращаемое значение метода в качестве выходных данных:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

В следующем примере показано, как использовать интерфейс `IAsyncCollector` для отправки пакета сообщений.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

В следующем примере показана привязка данных сетки событий к выходной привязке в файле *Function. JSON* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Ниже приведен C# код сценария, который создает одно событие:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Ниже приведен C# код сценария, создающий несколько событий:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

В следующем примере показана привязка данных сетки событий к выходной привязке в файле *Function. JSON* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Вот код JavaScript, создающий одно событие:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Ниже приведен код JavaScript, создающий несколько событий:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Выходная привязка сетки событий недоступна для Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Выходная привязка сетки событий недоступна для Java.

---

## <a name="attributes-and-annotations"></a>Атрибуты и заметки

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Для [ C# библиотек классов](functions-dotnet-class-library.md)используйте атрибут [евентгридаттрибуте](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

Конструктор атрибута принимает имя параметра приложения, которое содержит имя пользовательского раздела, и имя параметра приложения, содержащего ключ раздела. Дополнительные сведения об этих параметрах см. в разделе [Привязки концентраторов событий функций Azure](#configuration). Ниже приведен пример атрибута `EventGrid`.

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Полный пример см. в разделе [Пример](#example).

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

Атрибуты не поддерживаются C# сценарием.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются в JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Выходная привязка сетки событий недоступна для Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Выходная привязка сетки событий недоступна для Java.

---

## <a name="configuration"></a>Конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `EventGrid`.

|свойство function.json | Свойство атрибута |Description|
|---------|---------|----------------------|
|**type** | Недоступно | Необходимо задать значение "eventGrid". |
|**direction** | Недоступно | Для этого свойства необходимо задать значение out. Этот параметр задается автоматически при создании привязки на портале Azure. |
|**name** | Недоступно | Имя переменной, используемое в коде функции, которая представляет событие. |
|**топицендпоинтури** |**топицендпоинтури** | Имя параметра приложения, содержащего универсальный код ресурса (URI) для пользовательского раздела, например `MyTopicEndpointUri`. |
|**топиккэйсеттинг** |**топиккэйсеттинг** | Имя параметра приложения, содержащего ключ доступа для пользовательского раздела. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Убедитесь, что в качестве значения свойства конфигурации `TopicEndpointUri` задано имя параметра приложения, содержащего универсальный код ресурса (URI) пользовательского раздела. Не указывайте URI пользовательского раздела непосредственно в этом свойстве.

## <a name="usage"></a>Использование

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Отправка сообщений с помощью параметра метода, например `out EventGridEvent paramName`. Для записи нескольких сообщений можно использовать `ICollector<EventGridEvent>` или `IAsyncCollector<EventGridEvent>` вместо `out EventGridEvent`.

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

Отправка сообщений с помощью параметра метода, например `out EventGridEvent paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Для записи нескольких сообщений можно использовать `ICollector<EventGridEvent>` или `IAsyncCollector<EventGridEvent>` вместо `out EventGridEvent`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Получите доступ к выходному событию с помощью `context.bindings.<name>`, где `<name>` — это значение, указанное в свойстве `name` файла *Function. JSON*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Выходная привязка сетки событий недоступна для Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Выходная привязка сетки событий недоступна для Java.

---

## <a name="next-steps"></a>Дальнейшие действия

* [Отправка события сетки событий](./functions-bindings-event-grid-trigger.md)
