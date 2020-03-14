---
title: Использование службы "Сетка событий Azure" с событиями в схеме CloudEvents
description: Описание использования схемы Клаудевентс для событий в службе "Сетка событий Azure". Служба поддерживает события в реализации в JSON облачных событий.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 25a24c5bb44c77038a508e4c2f4e099132101f6a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265081"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Использование схемы Клаудевентс v 1.0 со службой "Сетка событий"

В дополнение к [схеме событий по умолчанию](event-schema.md)служба "Сетка событий Azure" изначально поддерживает события в [реализации JSON клаудевентс v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) и [привязки протокола HTTP](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) — [открытая спецификация](https://github.com/cloudevents/spec/blob/v1.0/spec.md) для описания данных о событиях.

CloudEvents упрощает взаимодействие, предоставляя общую схему событий для публикации и использования событий на основе облака. Эта схема предоставляет обычные средства, стандартные способы маршрутизации и обработки событий, а также универсальные методы десериализации внешней схемы событий. Общая схема позволяет легко интегрировать работу на разных платформах.

Создание CloudEvents — это [совместная работа](https://github.com/cloudevents/spec/blob/master/community/contributors.md) нескольких компаний, включая корпорацию Майкрософт и компанию [Cloud Native Computing Foundation](https://www.cncf.io/). В настоящее время он доступен в версии 1,0.

В этой статье описывается использование схемы CloudEvents со службой "Сетка событий".

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Установка предварительной версии функции

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Схема CloudEvent

Ниже приведен пример события хранилища BLOB-объектов Azure в формате CloudEvents.

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Подробное описание доступных полей, их типов и определений в Клаудевентс v 1.0 [можно найти здесь](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Значения заголовков для событий, доставляемых в схеме CloudEvents и схеме "Сетка событий", совпадают, за исключением элемента `content-type`. Для схемы CloudEvents значение заголовка — `"content-type":"application/cloudevents+json; charset=utf-8"`. Для схемы "Сетка событий" значение заголовка — `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Настройка сетки событий для CloudEvents

Службу "Сетка событий" можно использовать для ввода и вывода событий в схеме CloudEvents. Кроме того, CloudEvents можно использовать для системных событий, таких как события хранилища BLOB-объектов и Центра Интернета вещей, а также настраиваемые события. CloudEvents также может преобразовать эти события (в другой формат и обратно) при передаче по сети.


| Схема ввода       | Схема вывода
|--------------------|---------------------
| Формат CloudEvents | Формат CloudEvents
| Формат сетки событий  | Формат CloudEvents
| Формат сетки событий  | Формат сетки событий

Для всех схем событий служба "Сетка событий" требует проверки при публикации в разделе сетки событий и создании подписки на события. Дополнительные сведения см. в разделе [Сетка событий: безопасность и проверка подлинности](security-authentication.md).

### <a name="input-schema"></a>Схема ввода

Схема ввода для пользовательского раздела указывается при создании этого раздела.

Для интерфейса командной строки Azure:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Для PowerShell используйте команду:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Схема вывода

Схема вывода указывается при создании подписки на событие.

Для интерфейса командной строки Azure:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Для PowerShell используйте команду:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Сейчас, если событие доставляется в схему CloudEvents, триггер службы "Сетка событий" нельзя использовать в приложении "Функции Azure". Используйте триггер HTTP. Примеры реализации триггера HTTP, который получает события в схеме Клаудевентс, см. в разделе [Использование клаудевентс с функциями Azure](#azure-functions).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Проверка конечной точки с помощью Клаудевентс v 1.0

Если вы уже знакомы со службой "Сетка событий", то, возможно, знаете, что подтверждение конечной точки сетки событий не позволяет избежать нарушения. Клаудевентс v 1.0 реализует собственную [семантику защиты от](security-authentication.md#webhook-event-delivery) нарушений с помощью метода HTTP Options. Подробнее об этом можно прочитать [здесь](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). При использовании схемы Клаудевентс для вывода в службе "Сетка событий" используется с защитой Клаудевентс v 1.0 вместо механизма событий проверки сетки событий.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Использование с функциями Azure

[Привязка сетки событий функций Azure](../azure-functions/functions-bindings-event-grid.md) изначально не поддерживает клаудевентс, поэтому для чтения сообщений клаудевентс используются функции, активируемые HTTP. При использовании триггера HTTP для чтения Клаудевентс необходимо написать код для автоматического запуска триггера сетки событий:

* Отправляет запрос проверки в [запрос подтверждения подписки](../event-grid/security-authentication.md#webhook-event-delivery).
* Вызывает функцию один раз для каждого элемента массива событий, содержащихся в тексте запроса.

Дополнительные сведения об URL-адресе, используемом для вызова функции, выполняемой локально или в Azure, см. в [справочной документации по привязке триггера HTTP](../azure-functions/functions-bindings-http-webhook.md).

Следующий пример кода C# для триггера HTTP имитирует поведение триггера службы "Сетка событий".  Этот пример можно использовать для событий, доставленных в схеме CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Следующий пример кода JavaScript для триггера HTTP имитирует поведение триггера службы "Сетка событий". Этот пример можно использовать для событий, доставленных в схеме CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
    context.done();
};
```

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со сведениями о [мониторинге доставки сообщений в службе "Сетка событий"](monitor-event-delivery.md).
* Мы рекомендуем вам протестировать CloudEvents и [внести свой вклад](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) в усовершенствование схемы, оставляя комментарии.
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
