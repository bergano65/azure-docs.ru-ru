---
title: Выходная привязка сетки событий Azure для функций Azure
description: Узнайте, как отправить событие службы "Сетка событий" в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 0237bcbf98578d9f83f3c9652661c786df54e73a
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627693"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Выходная привязка сетки событий Azure для функций Azure

Используйте выходную привязку сетки событий для записи событий в пользовательский раздел. Необходимо иметь допустимый [ключ доступа для пользовательского раздела](../event-grid/security-authentication.md#authenticate-publishing-clients-using-sas-or-key).

Дополнительные сведения об установке и сведениях о конфигурации см. в [обзоре](./functions-bindings-event-grid.md).

> [!NOTE]
> Выходная привязка сетки событий не поддерживает подписанные URL-адрес (токены SAS). Необходимо использовать ключ доступа раздела.

> [!IMPORTANT]
> Выходная привязка сетки событий доступна только для функций 2. x и более поздних версий.

## <a name="example"></a>Пример

# <a name="c"></a>[C#](#tab/csharp)

В следующем примере показана [функция C#](functions-dotnet-class-library.md) , которая записывает сообщение в пользовательский раздел сетки событий, используя возвращаемое значение метода в качестве выходных данных:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

В следующем примере показано, как использовать `IAsyncCollector` интерфейс для отправки пакета сообщений.

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

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

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

Ниже приведен код сценария C#, который создает одно событие:

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

Ниже приведен код сценария C#, который создает несколько событий:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

Выходная привязка сетки событий недоступна для Python.

# <a name="java"></a>[Java](#tab/java)

Выходная привязка сетки событий недоступна для Java.

---

## <a name="attributes-and-annotations"></a>Атрибуты и заметки

# <a name="c"></a>[C#](#tab/csharp)

Для [библиотек классов C#](functions-dotnet-class-library.md)используйте атрибут [евентгридаттрибуте](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

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

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Атрибуты не поддерживаются скриптом C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются в JavaScript.

# <a name="python"></a>[Python](#tab/python)

Выходная привязка сетки событий недоступна для Python.

# <a name="java"></a>[Java](#tab/java)

Выходная привязка сетки событий недоступна для Java.

---

## <a name="configuration"></a>Параметр Configuration

В следующей таблице описаны свойства конфигурации привязки, заданные в файле *Function. JSON* и `EventGrid` атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | н/д | Необходимо задать значение "eventGrid". |
|**двух** | н/д | Для этого свойства необходимо задать значение out. Этот параметр задается автоматически при создании привязки на портале Azure. |
|**name** | н/д | Имя переменной, используемое в коде функции, которая представляет событие. |
|**топицендпоинтури** |**топицендпоинтури** | Имя параметра приложения, содержащего универсальный код ресурса (URI) для пользовательского раздела, например `MyTopicEndpointUri`. |
|**топиккэйсеттинг** |**топиккэйсеттинг** | Имя параметра приложения, содержащего ключ доступа для пользовательского раздела. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Убедитесь, что в качестве значения свойства `TopicEndpointUri` конфигурации задано имя параметра приложения, содержащего универсальный код ресурса (URI) пользовательского раздела. Не указывайте URI пользовательского раздела непосредственно в этом свойстве.

## <a name="usage"></a>Использование

# <a name="c"></a>[C#](#tab/csharp)

Отправка сообщений с помощью параметра метода, например `out EventGridEvent paramName`. Для записи нескольких сообщений можно использовать `ICollector<EventGridEvent>` или `IAsyncCollector<EventGridEvent>` вместо `out EventGridEvent`.

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Отправка сообщений с помощью параметра метода, например `out EventGridEvent paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Для записи нескольких сообщений можно использовать `ICollector<EventGridEvent>` или `IAsyncCollector<EventGridEvent>` вместо `out EventGridEvent`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Получить доступ к выходному `context.bindings.<name>` событию `<name>` с помощью WHERE — это значение `name` , указанное в свойстве файла *Function. JSON*.

# <a name="python"></a>[Python](#tab/python)

Выходная привязка сетки событий недоступна для Python.

# <a name="java"></a>[Java](#tab/java)

Выходная привязка сетки событий недоступна для Java.

---

## <a name="next-steps"></a>Дальнейшие действия

* [Отправка события сетки событий](./functions-bindings-event-grid-trigger.md)
