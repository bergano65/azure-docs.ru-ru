---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589744"
---
Используйте выходную привязку Центров событий для записи событий в поток событий. Чтобы записывать события в концентратор событий, необходимо иметь разрешение на оправку в него событий.

Убедитесь, что необходимые ссылки на пакет имеются перед попыткой реализовать выходную привязку.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

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

В следующем примере показано, как использовать `IAsyncCollector` интерфейс для отправки пакета сообщений. Этот сценарий является общим, когда вы обрабатываете сообщения, поступающие из одного концентратора событий, и отправляете результат в другой концентратор событий.

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

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция сценария C#](../articles/azure-functions/functions-reference-csharp.md), которая использует эту привязку. Эта функция записывает сообщение в концентратор событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. Первый пример предназначен для функций 2.x и выше, а второй — для функций 1.x. 

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция JavaScript](../articles/azure-functions/functions-reference-node.md), которая использует эту привязку. Эта функция записывает сообщение в концентратор событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. Первый пример предназначен для функций 2.x и выше, а второй — для функций 1.x. 

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

В следующем примере показана функция Java, которая записывает сообщение, содержащее текущее время, в концентратор событий.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@EventHubOutput` для параметров, значения которых будут опубликованы в концентраторе событий.  Параметр должен быть типа `OutputBinding<T>`, где T — POJO или любой собственный тип Java.

---

## <a name="attributes-and-annotations"></a>Атрибуты и аннотации

# <a name="c"></a>[C #](#tab/csharp)

В [библиотеках классов C#](../articles/azure-functions/functions-dotnet-class-library.md) используйте атрибут [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs).

Конструктор атрибута принимает имя концентратора событий и имя параметра приложения, содержащего строку подключения. Дополнительные сведения об этих параметрах см. в разделе [Привязки концентраторов событий функций Azure](#configuration). Ниже приведен пример атрибута `EventHub`.

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
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

Атрибуты не поддерживаются Python.

# <a name="java"></a>[Java](#tab/java)

В [библиотеке выполнения функций Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)используйте аннотацию [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) по параметрам, значение которых будет опубликовано в Концентраторе событий. Параметр должен быть `OutputBinding<T>` типа, где `T` находится POJO или любой родной тип Java.

---

## <a name="configuration"></a>Параметр Configuration

В следующей таблице объясняется свойства связывающей конфигурации, `EventHub` установленные в файле *function.json* и атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**тип** | Недоступно | Для этого свойства необходимо задать значение "eventHub" |
|**direction** | Недоступно | Для этого свойства необходимо задать значение out. Этот параметр задается автоматически при создании привязки на портале Azure. |
|**name** | Недоступно | Имя переменной, используемое в коде функции, которая представляет событие. |
|**Путь** |**EventHubName** | Только служба "Функции" версии 1.x. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. |
|**eventHubName** |**EventHubName** | Функции 2.x и выше. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. |
|**Подключения** |**Подключения** | Имя параметра приложения, содержащего строку подключения к пространству имен концентратора событий. Скопируйте эту строку подключения, нажав кнопку **Сведения о подключении** для *пространства имен*, а не сам концентратор событий. Чтобы отправлять сообщения в поток событий, эта строка подключения должна иметь разрешения на отправку.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Использование

# <a name="c"></a>[C #](#tab/csharp)

Отправка сообщений с помощью параметра метода, такого как `out string paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Для записи нескольких сообщений можно использовать `ICollector<string>` или `IAsyncCollector<string>` вместо `out string`.

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Отправка сообщений с помощью параметра метода, такого как `out string paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Для записи нескольких сообщений можно использовать `ICollector<string>` или `IAsyncCollector<string>` вместо `out string`.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Доступ к событию `context.bindings.<name>` `<name>` вывода, используя, `name` где значение, указанное в свойстве *function.json*.

# <a name="python"></a>[Python](#tab/python)

Существует два варианта выхода сообщения концентратора событий из функции:

- **Значение возврата**: `name` Установите свойство `$return`в *function.json* к . При этой конфигурации значение возврата функции сохраняется как сообщение концентратора событий.

- **Императив**: Передайте значение [установленного](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) методу параметра, объявленного как тип [Out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Передаваемые `set` значения сохраняются в виде сообщения концентратора событий.

# <a name="java"></a>[Java](#tab/java)

Существует два варианта вывода сообщения концентратора событий из функции с помощью аннотации [EventHubOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)

- **Значение возврата**: Приприменении аннотации к самой функции, значение возврата функции сохраняется как сообщение концентратора событий.

- **Императив**: Чтобы явно установить значение сообщения, примените аннотацию к определенному параметру типа, [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)где `T` находится POJO или любой родной тип Java. В этой конфигурации передача `setValue` значения методу сохраняетзначение в виде сообщения концентратора событий.

---

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочник |
|---|---|
| Концентратор событий | [Руководство по операциям](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Параметры файла host.json

В этом разделе описаны глобальные настройки конфигурации, доступные для этого привязки в версиях 2.x и выше. Приведенный ниже файл «Пример host.json» содержит только настройки версии 2.x для этой привязки. Для получения дополнительной информации о настройках глобальной конфигурации в версиях 2.x и далее см. [ссылку host.json на функции Azure.](../articles/azure-functions/functions-host-json.md)

> [!NOTE]
> Чтобы получить дополнительные сведения о файле host.json в Функции 1.x, см. статью [host.json reference for Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md)(Справочник по файлу host.json для службы "Функции Azure" версии 1.x.).

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

|Свойство  |Значение по умолчанию | Описание |
|---------|---------|---------|
|`maxBatchSize`|10|Максимальное число событий, получаемых в цикле получения.|
|`prefetchCount`|300|По умолчанию предварительный подсчет, `EventProcessorHost`используемый базовым .|
|`batchCheckpointFrequency`|1|Количество пакетов событий, которые необходимо обработать перед созданием контрольной точки курсора EventHub.|
