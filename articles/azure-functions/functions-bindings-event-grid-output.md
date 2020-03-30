---
title: Привязка выходной сетки событий Azure для функций Azure
description: Научитесь отправлять событие Event Grid в Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368952"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Привязка выходной сетки событий Azure для функций Azure

Используйте привязку вывода Event Grid для записи событий на пользовательскую тему. Вы должны иметь действительный [ключ доступа для пользовательской темы.](../event-grid/security-authentication.md#custom-topic-publishing)

Для получения информации о настройке и деталях конфигурации, [см.](./functions-bindings-event-grid.md)

> [!NOTE]
> Привязка к выходу Event Grid не поддерживает общие подписи доступа (токены SAS). Вы должны использовать ключ доступа темы.

> [!IMPORTANT]
> Привязка выходной сетки событий доступна только для функций 2.x и выше.

## <a name="example"></a>Пример

# <a name="c"></a>[C #](#tab/csharp)

В следующем примере [показана функция C-,](functions-dotnet-class-library.md) которая записывает сообщение на пользовательскую тему Event Grid, используя значение возврата метода в качестве вывода:

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

В следующем примере показаны данные связывания выходной сетки событий в файле *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Вот код скрипта C', который создает одно событие:

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

Вот код скрипта C', который создает несколько событий:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

В следующем примере показаны данные связывания выходной сетки событий в файле *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Вот код JavaScript, который создает одно событие:

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

Вот код JavaScript, который создает несколько событий:

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

Привязка вывода Event Grid недоступна для Python.

# <a name="java"></a>[Java](#tab/java)

Привязка вывода Event Grid недоступна для Java.

---

## <a name="attributes-and-annotations"></a>Атрибуты и аннотации

# <a name="c"></a>[C #](#tab/csharp)

Для [библиотек класса СЗ](functions-dotnet-class-library.md)используйте атрибут [EventGridAttribute.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs)

Конструктор атрибута получает название настройки приложения, содержащей название пользовательской темы, и название настройки приложения, содержащей ключ темы. Дополнительные сведения об этих параметрах см. в разделе [Привязки концентраторов событий функций Azure](#configuration). Ниже приведен пример атрибута `EventGrid`.

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Для полного примера [см.](#example)

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Атрибуты не поддерживаются скриптом C'.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Атрибуты не поддерживаются JavaScript.

# <a name="python"></a>[Python](#tab/python)

Привязка вывода Event Grid недоступна для Python.

# <a name="java"></a>[Java](#tab/java)

Привязка вывода Event Grid недоступна для Java.

---

## <a name="configuration"></a>Параметр Configuration

В следующей таблице объясняется свойства связывающей конфигурации, `EventGrid` установленные в файле *function.json* и атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**тип** | Недоступно | Должен быть установлен на "eventGrid". |
|**direction** | Недоступно | Для этого свойства необходимо задать значение out. Этот параметр задается автоматически при создании привязки на портале Azure. |
|**name** | Недоступно | Имя переменной, используемое в коде функции, которая представляет событие. |
|**topicEndpointUri** |**TopicEndpointuri** | Название параметра приложения, содержащего URI для пользовательской темы, `MyTopicEndpointUri`например. |
|**topicKeySetting** |**ТемаКейКснастройка** | Название параметра приложения, содержащего ключ доступа к пользовательской теме. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Убедитесь, что вы `TopicEndpointUri` установите значение свойства конфигурации к названию настройки приложения, содержащей URI пользовательской темы. Не указывая URI пользовательской темы непосредственно в этом свойстве.

## <a name="usage"></a>Использование

# <a name="c"></a>[C #](#tab/csharp)

Отправка сообщений с помощью параметра метода, такого как `out EventGridEvent paramName`. Для записи нескольких сообщений можно использовать `ICollector<EventGridEvent>` или `IAsyncCollector<EventGridEvent>` вместо `out EventGridEvent`.

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Отправка сообщений с помощью параметра метода, такого как `out EventGridEvent paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Для записи нескольких сообщений можно использовать `ICollector<EventGridEvent>` или `IAsyncCollector<EventGridEvent>` вместо `out EventGridEvent`.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Доступ к событию `context.bindings.<name>` `<name>` вывода, используя, `name` где значение, указанное в свойстве *function.json*.

# <a name="python"></a>[Python](#tab/python)

Привязка вывода Event Grid недоступна для Python.

# <a name="java"></a>[Java](#tab/java)

Привязка вывода Event Grid недоступна для Java.

---

## <a name="next-steps"></a>Дальнейшие действия

* [Отправка события сетки событий](./functions-bindings-event-grid-trigger.md)
