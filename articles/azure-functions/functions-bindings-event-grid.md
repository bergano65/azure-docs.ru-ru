---
title: Привязки службы "Сетка событий Azure" для функций Azure
description: Узнайте, как обрабатывать события службы "Сетка событий" в службе "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 02/03/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: df851a79ef3fbb7473e100619f58b7f35bce1d45
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212011"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Привязки службы "Сетка событий Azure" для функций Azure

В этом руководстве показано, как обрабатывать события службы [Сетка событий](../event-grid/overview.md) в службе "Функции Azure". Дополнительные сведения о том, как управлять сообщениями сетки событий в конечной точке HTTP, см. в статье [получение событий в конечной точке](../event-grid/receive-events.md)HTTP.

Служба "Сетка событий" — это служба Azure, которая отправляет HTTP-запросы для уведомления о событиях, которые происходят в *издателях*. Издатель — это служба или ресурс, в котором происходит событие. Например, учетная запись хранения больших двоичных объектов Azure является издателем, а [отправка или удаление большого двоичного объекта — это событие](../storage/blobs/storage-blob-event-overview.md). Некоторые [службы Azure имеют встроенную поддержку публикации событий в службу "Сетка событий"](../event-grid/overview.md#event-sources).

*Обработчики* событий получают и обрабатывают события. Служба "Функции Azure" является одной из нескольких [служб Azure, в которых есть встроенная поддержка обработки событий Сетки событий](../event-grid/overview.md#event-handlers). В этой статье вы узнаете, как использовать триггер сетки событий для вызова функции при получении события из сетки событий и использовать выходную привязку для отправки событий в [пользовательский раздел сетки событий](../event-grid/post-to-custom-topic.md).

При желании можно использовать триггер HTTP для управления событиями сетки событий. см. раздел [получение событий для конечной точки HTTP](../event-grid/receive-events.md). Сейчас, если событие доставляется в [схему CloudEvents](../event-grid/cloudevents-schema.md#azure-functions), триггер службы "Сетка событий" нельзя использовать в приложении "Функции Azure". В этом случае необходимо использовать триггер HTTP.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages — функции 2. x и более поздних версий

Привязки службы "Сетка событий" предоставляются в пакете NuGet [Microsoft. Azure. веб-заданий. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) , версия 2. x. Исходный код для пакета находится в репозитории GitHub [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Триггер службы "Сетка событий" предоставляется в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) версии 1.х. Исходный код для пакета находится в репозитории GitHub [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Триггер

Используйте триггер функции для реагирования на событие, отправленное в сетку событий.

## <a name="trigger---example"></a>Пример триггера

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Пример триггера HTTP см. [в разделе Receive Events to a HTTP Endpoint](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C#(2. x и выше)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая выполняет привязку к `EventGridEvent`:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Ознакомьтесь с дополнительными сведениями о [пакетах](#trigger---attributes), [конфигурации](#trigger---configuration) и [использовании](#trigger---usage).

### <a name="version-1x"></a>Версия 1.x

В следующем примере показана [функция C#](functions-dotnet-class-library.md) службы "Функции" 1.х, которая выполняет привязку к `JObject`.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

В следующем примере показаны привязка триггера в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку.

Данные привязки в файле *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-script-version-2x-and-higher"></a>C#Скрипт (версия 2. x и более поздние)

Ниже приведен пример, который привязывается к `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Ознакомьтесь с дополнительными сведениями о [пакетах](#trigger---attributes), [конфигурации](#trigger---configuration) и [использовании](#trigger---usage).

### <a name="version-1x"></a>Версия 1.x

Ниже приведен код сценария C# службы "Функции" 1.х, который выполняет привязку к `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

В следующем примере показана привязка триггера в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку.

Данные привязки в файле *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

В следующем примере показаны привязка триггера в файле *function.json* и [функция Python](functions-reference-python.md), использующая эту привязку.

Данные привязки в файле *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Ниже приведен код Python.

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Этот раздел содержит следующие примеры.

* [Триггер сетки событий, строковый параметр](#event-grid-trigger-string-parameter)
* [Триггер сетки событий, параметр POJO](#event-grid-trigger-pojo-parameter)

В следующих примерах показана привязка триггера в [Java](functions-reference-java.md) , использующая привязку и выводимое на печать событие, сначала получающая событие как `String`, а второе — как POJO.

### <a name="event-grid-trigger-string-parameter"></a>Триггер сетки событий, параметр строки

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Триггер сетки событий, параметр POJO

В этом примере используется следующий POJO, представляющий свойства верхнего уровня события сетки событий.

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

По прибытии полезные данные JSON события десериализуются в ```EventSchema``` POJO для использования функцией. Это позволяет функции использовать свойства событий в объектно-ориентированном стиле.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

В [библиотеке выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `EventGridTrigger` для параметров, значение которых должно быть получено от EventGrid. Параметры с этими заметками запускают функцию, когда происходит событие.  Эта заметка может использоваться с собственными типами Java, объектами POJO или значениями, допускающими значения NULL, используя `Optional<T>`.

---

## <a name="trigger---attributes"></a>Атрибуты триггера

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs).

Вот как выглядит атрибут `EventGridTrigger` в сигнатуре метода:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Полный пример см. в разделе "Пример C#".

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

Атрибуты не поддерживаются C# сценарием.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются в JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Атрибуты не поддерживаются в Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Заметка [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) позволяет декларативно настроить привязку сетки событий, предоставляя значения конфигурации. Дополнительные сведения см. в разделах [примеры](#trigger---example) и [Конфигурация](#trigger---configuration) .

---

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json*. В атрибуте `EventGridTrigger` не задаются параметры или свойства конструкции.

|свойство function.json |Описание|
|---------|---------|
| **type** | Обязательное. Необходимо задать значение `eventGridTrigger`. |
| **direction** | Обязательное. Необходимо задать значение `in`. |
| **name** | Обязательное. Имя переменной, используемой в коде функции, для параметра, получающего данные события. |

## <a name="trigger---usage"></a>Использование триггера

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

В функциях Azure 1. x можно использовать следующие типы параметров для триггера службы "Сетка событий":

* `JObject`
* `string`

В функциях Azure 2. x и более поздних версий вы также можете использовать для триггера службы "Сетка событий" следующий тип параметра:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent` — определяет свойства для полей, которые являются общими для всех типов событий.

> [!NOTE]
> В службе "Функции" 1.х при попытке сделать привязку к `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` в компиляторе появится сообщение о том, что это не рекомендуется, и совет использовать `Microsoft.Azure.EventGrid.Models.EventGridEvent`. Чтобы использовать более новый тип, ссылайтесь на пакет NuGet [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) и полностью определите имя типа `EventGridEvent`, добавив префикс `Microsoft.Azure.EventGrid.Models`.

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

В функциях Azure 1. x можно использовать следующие типы параметров для триггера службы "Сетка событий":

* `JObject`
* `string`

В функциях Azure 2. x и более поздних версий вы также можете использовать для триггера службы "Сетка событий" следующий тип параметра:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent` — определяет свойства для полей, которые являются общими для всех типов событий.

> [!NOTE]
> В службе "Функции" 1.х при попытке сделать привязку к `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` в компиляторе появится сообщение о том, что это не рекомендуется, и совет использовать `Microsoft.Azure.EventGrid.Models.EventGridEvent`. Чтобы использовать более новый тип, ссылайтесь на пакет NuGet [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) и полностью определите имя типа `EventGridEvent`, добавив префикс `Microsoft.Azure.EventGrid.Models`. Сведения о том, как ссылаться на пакеты NuGet в функции сценария C#, см. в разделе [об использовании пакетов NuGet](functions-reference-csharp.md#using-nuget-packages).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Экземпляр сетки событий доступен через параметр, настроенный в свойстве `name` файла *Function. JSON* .

# <a name="pythontabpython"></a>[Python](#tab/python)

Экземпляр сетки событий доступен через параметр, настроенный в свойстве `name` файла *Function. JSON* , типизированный как `func.EventGridEvent`.

# <a name="javatabjava"></a>[Java](#tab/java)

Экземпляр события сетки событий доступен через параметр, связанный с атрибутом `EventGridTrigger`, который типизирован как `EventSchema`. Дополнительные сведения см. в [примере](#trigger---example) .

---

## <a name="trigger---event-schema"></a>Схема события триггера

Данные для события службы "Сетка событий" получаются в качестве объекта JSON в теле HTTP-запроса. JSON должен быть аналогичным приведенному ниже:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Показанный пример является массивом из одного элемента. Сетка событий всегда отправляет массив, который может содержать более одного события. Среда выполнения вызывает функцию один раз для каждого элемента массива.

Свойства верхнего уровня в данных JSON события одинаковы для всех типов событий, тогда как содержимое свойства `data` определено для каждого типа события. Пример показан для события хранилища BLOB-объектов.

Описания распространенных и связанных с событием свойств см. в разделе [Свойства события](../event-grid/event-schema.md#event-properties) в документации по службе "Сетка событий".

Тип `EventGridEvent` определяет только свойства верхнего уровня. Свойство `Data` имеет значение `JObject`.

## <a name="trigger---create-a-subscription"></a>Триггер — создание подписки

Чтобы начать получать HTTP-запросы Сетки событий, создайте подписку службы "Сетка событий", которая указывает URL-адрес конечной точки, вызывающей функцию.

### <a name="azure-portal"></a>Портал Azure

Для функций, разрабатываемых на портале Azure с использованием триггера службы "Сетка событий", выберите **Добавить подписку сетки событий**.

![Создание подписки на портале](media/functions-bindings-event-grid/portal-sub-create.png)

Когда вы щелкните эту ссылку, откроется страница **создания подписки на событие** с автоматически заполненным URL-адресом конечной точки.

![Автоматически заполненный URL-адрес конечной точки](media/functions-bindings-event-grid/endpoint-url.png)

Дополнительные сведения о создании подписок с помощью портала Azure см. в разделе [Создание и перенаправление пользовательских событий с помощью портала Azure и службы "Сетка событий"](../event-grid/custom-event-quickstart-portal.md) документации по службе "Сетка событий".

### <a name="azure-cli"></a>Azure CLI

Чтобы создать подписку с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), используйте команду [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create).

Для команды нужен URL-адрес конечной точки, который вызывает функцию. В следующем примере показан шаблон URL-адреса в зависимости от версии:

#### <a name="version-2x-and-higher-runtime"></a>Среда выполнения версии 2. x (и выше)

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Среда выполнения версии 1.x

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

Системный ключ — это ключ авторизации, который необходимо включить в URL-адрес конечной точки для триггера службы "Сетка событий". В следующем разделе показано, как получить системный ключ.

Ниже приведен пример, который подписывается на учетную запись хранения больших двоичных объектов (с заполнителем для системного ключа):

#### <a name="version-2x-and-higher-runtime"></a>Среда выполнения версии 2. x (и выше)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Среда выполнения версии 1.x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Дополнительные сведения о создании подписки см. в разделе [Примеры использования хранилища больших двоичных объектов](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) или других коротких руководствах по использованию службы "Сетка событий".

### <a name="get-the-system-key"></a>Получение системного ключа

Системный ключ можно получить с помощью следующего API-интерфейса (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Среда выполнения версии 2. x (и выше)

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Среда выполнения версии 1.x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Это API администратора, поэтому для него нужен [главный ключ](functions-bindings-http-webhook-trigger.md#authorization-keys) приложения-функции. Не путайте системный ключ (для вызова функции триггера службы "Сетка событий") с главным ключом (для выполнения административных задач в приложении-функции). Во время оформления подписки на раздел службы "Сетка событий" необходимо использовать системный ключ.

Ниже приведен пример ответа, который предоставляет системный ключ:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Можно получить главный ключ для приложения-функции на вкладке **Параметры приложения-функции** на портале.

> [!IMPORTANT]
> Главный ключ предоставляет административный доступ к приложению-функции. Не передавайте этот ключ третьим лицам и не распространяйте его в собственных клиентских приложениях.

Дополнительные сведения см. в разделе [Ключи авторизации](functions-bindings-http-webhook-trigger.md#authorization-keys) в справочнике по триггерам HTTP.

Кроме того, вы можете отправить HTTP-запрос PUT, чтобы самостоятельно указать значение ключа.

## <a name="trigger---local-testing-with-viewer-web-app"></a>Запуск локального тестирования с помощью средства просмотра веб-приложения

Чтобы протестировать триггер службы "Сетка событий" локально, необходимо отправить HTTP-запросы службы "Сетка событий", переданные из своего источника в облаке, на локальный компьютер. Для этого можно записать запросы в сети и вручную отправить их на локальный компьютер:

1. [Создайте веб-приложение средства просмотра](#create-a-viewer-web-app), которое собирает сообщения о событиях.
1. [Создайте подписку службы "Сетка событий"](#create-an-event-grid-subscription), которая отправляет события в приложение средства просмотра.
1. [Создайте запрос](#generate-a-request) и скопируйте текст запроса из приложения средства просмотра.
1. [Вручную вставьте запрос](#manually-post-the-request) в URL-адрес localhost функции триггера службы "Сетка событий".

Закончив тестирование, можно использовать ту же подписку для рабочей среды, обновив конечную точку. Используйте команду Azure CLI [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update).

### <a name="create-a-viewer-web-app"></a>Создание веб-приложения средства просмотра

Чтобы упростить запись сообщений о событиях, разверните [готовое веб-приложение](https://github.com/Azure-Samples/azure-event-grid-viewer), которое отображает сообщения о событиях. Развернутое решение содержит план службы приложений, веб-приложение службы приложений и исходный код из GitHub.

Выберите **Развернуть в Azure**, чтобы развернуть решение в своей подписке. На портале Azure укажите значения остальных параметров.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Завершение развертывания может занять несколько минут. Когда развертывание успешно завершится, откройте веб-приложение и убедитесь, что оно работает. Откройте браузер и перейдите по адресу `https://<your-site-name>.azurewebsites.net`.

Вы увидите сайт без опубликованных событий.

![Представление нового сайта](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Создание подписки Сетки событий

Создайте подписку "Сетка событий" того типа, который требуется протестировать, и предоставьте URL-адрес из веб-приложения как конечную точку для уведомления о событиях. Конечная точка веб-приложения должна содержать суффикс `/api/updates/`. Таким образом, полный URL-адрес будет выглядеть следующим образом: `https://<your-site-name>.azurewebsites.net/api/updates`

Дополнительные сведения о создании подписок с помощью портала Azure см. в разделе [Создание пользовательского события с помощью портала Azure](../event-grid/custom-event-quickstart-portal.md) в документации по службе "Сетка событий".

### <a name="generate-a-request"></a>Создание запроса

Активируйте событие, которое будет создавать HTTP-трафик в конечную точку веб-приложения.  Например, если вы создали подписку хранилища BLOB-объектов, отправьте или удалите большой двоичный объект. Скопируйте текст запроса, когда запрос отобразится в веб-приложении.

Сначала будет получен запрос на подтверждение подписки. Игнорируйте такие запросы и скопируйте запрос события.

![Копирование текста запроса из веб-приложения](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Публикация запроса вручную

Запустите функцию службы "Сетка событий" локально.

Используйте такие средства, как [Postman](https://www.getpostman.com/) или [cURL](https://curl.haxx.se/docs/httpscripting.html), чтобы создать HTTP-запрос POST:

* Задайте заголовок `Content-Type: application/json`.
* Задайте заголовок `aeg-event-type: Notification`.
* Вставьте данные RequestBin в текст запроса.
* Опубликуйте по URL-адресу функции триггера сетки событий.
  * Для 2. x и более поздних версий используйте следующий шаблон:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Для использования с 1. x:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Для параметра `functionName` нужно указать имя, заданное в атрибуте `FunctionName`.

На следующих снимках экрана показаны заголовки и текст запроса в Postman:

![Заголовки в Postman](media/functions-bindings-event-grid/postman2.png)

![Текст запроса в Postman](media/functions-bindings-event-grid/postman.png)

Функция триггера службы "Сетка событий" выполняет и показывает журналы, похожие на приведенные ниже:

![Пример журналов функций триггера службы "Сетка событий"](media/functions-bindings-event-grid/eg-output.png)

## <a name="output"></a>Вывод

Используйте выходную привязку сетки событий для записи событий в пользовательский раздел. Необходимо иметь допустимый [ключ доступа для пользовательского раздела](../event-grid/security-authentication.md#custom-topic-publishing).

> [!NOTE]
> Выходная привязка сетки событий не поддерживает подписанные URL-адрес (токены SAS). Необходимо использовать ключ доступа раздела.

Прежде чем пытаться реализовать выходную привязку, убедитесь в наличии необходимых ссылок на пакеты.

> [!IMPORTANT]
> Выходная привязка сетки событий доступна только для функций 2. x и более поздних версий.

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

## <a name="output---attributes-and-annotations"></a>Выходные атрибуты и заметки

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Для [ C# библиотек классов](functions-dotnet-class-library.md)используйте атрибут [евентгридаттрибуте](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

Конструктор атрибута принимает имя параметра приложения, которое содержит имя пользовательского раздела, и имя параметра приложения, содержащего ключ раздела. Дополнительные сведения об этих параметрах см. в разделе [Привязки концентраторов событий функций Azure](#output---configuration). Ниже приведен пример атрибута `EventGrid`.

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Полный пример см. в разделе [Пример выходных данных C#](#output).

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

Атрибуты не поддерживаются C# сценарием.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются в JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Выходная привязка сетки событий недоступна для Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Выходная привязка сетки событий недоступна для Java.

---

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `EventGrid`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Недоступно | Необходимо задать значение "eventGrid". |
|**direction** | Недоступно | Для этого свойства необходимо задать значение out. Этот параметр задается автоматически при создании привязки на портале Azure. |
|**name** | Недоступно | Имя переменной, используемое в коде функции, которая представляет событие. |
|**топицендпоинтури** |**топицендпоинтури** | Имя параметра приложения, содержащего универсальный код ресурса (URI) для пользовательского раздела, например `MyTopicEndpointUri`. |
|**топиккэйсеттинг** |**топиккэйсеттинг** | Имя параметра приложения, содержащего ключ доступа для пользовательского раздела. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Убедитесь, что в качестве значения свойства конфигурации `TopicEndpointUri` задано имя параметра приложения, содержащего универсальный код ресурса (URI) пользовательского раздела. Не указывайте URI пользовательского раздела непосредственно в этом свойстве.

## <a name="output---usage"></a>Использование выходной привязки

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

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Общие сведения о службе "Сетка событий Azure"](../event-grid/overview.md)
