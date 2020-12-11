---
title: Использование службы "Сетка событий Azure" с событиями в схеме CloudEvents
description: В этой статье описана установка схемы CloudEvents для событий в службе "Сетка событий Azure". Служба поддерживает события в реализации в JSON CloudEvents.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: e13c3635da7e7a86f4fa2d31215303152167741c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109255"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Использование схемы CloudEvents версии 1.0 со службой "Сетка событий"
В дополнение к [схеме событий по умолчанию](event-schema.md), служба "Сетка событий Azure" поддерживает события в [реализации JSON схемы CloudEvents версии 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) и [привязку протокола HTTP](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) — [открытая спецификация](https://github.com/cloudevents/spec/blob/v1.0/spec.md) для описания данных о событиях.

CloudEvents упрощает взаимодействие, предоставляя общую схему событий для публикации и использования событий на основе облака. Эта схема предоставляет обычные средства, стандартные способы маршрутизации и обработки событий, а также универсальные методы десериализации внешней схемы событий. Общая схема позволяет легко интегрировать работу на разных платформах.

Создание CloudEvents — это [совместная работа](https://github.com/cloudevents/spec/blob/master/community/contributors.md) нескольких компаний, включая корпорацию Майкрософт и компанию [Cloud Native Computing Foundation](https://www.cncf.io/). В настоящее время доступна версия 1.0.

В этой статье описывается использование схемы CloudEvents со службой "Сетка событий".


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

Подробное описание доступных полей, их типов и определений в CloudEvents версии 1.0 [можно изучить здесь](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Значения заголовков для событий, доставляемых в схеме CloudEvents и схеме "Сетка событий", совпадают, за исключением элемента `content-type`. Для схемы CloudEvents значение заголовка — `"content-type":"application/cloudevents+json; charset=utf-8"`. Для схемы "Сетка событий" значение заголовка — `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Настройка сетки событий для CloudEvents

Вы можете использовать сетку событий для ввода и вывода событий в схеме Клаудевентс. В следующей таблице описаны возможные преобразования.

 Ресурс сетки событий | Схема ввода       | Схема доставки
|---------------------|-------------------|---------------------
| Разделы о системе       | Схема сетки событий | Схема сетки событий или схема Клаудевент
| Разделы и домены пользователя | Схема сетки событий | Схема сетки событий
| Разделы и домены пользователя | Схема Клаудевент | Схема Клаудевент
| Разделы и домены пользователя | Пользовательская схема     | Пользовательская схема или схема сетки событий или схема Клаудевент
| партнертопикс       | Схема Клаудевент | Схема Клаудевент


Для всех схем событий сетка событий требует проверки при публикации в разделе сетки событий и при создании подписки на события.

Дополнительные сведения см. в разделе [Сетка событий: безопасность и проверка подлинности](security-authentication.md).

### <a name="input-schema"></a>Схема ввода

Схема ввода для пользовательского раздела указывается при создании этого раздела.

Для интерфейса командной строки Azure:

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Для PowerShell используйте команду:

```azurepowershell-interactive
New-AzEventGridTopic `
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
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Сейчас, если событие доставляется в схему CloudEvents, триггер службы "Сетка событий" нельзя использовать в приложении "Функции Azure". Используйте триггер HTTP. Примеры реализации триггера HTTP, который получает события в схеме CloudEvents, см. в разделе [Использование CloudEvents с Функциями Azure](#azure-functions).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Проверка конечной точки с помощью CloudEvents версии 1.0

Если вы уже знакомы со службой "Сетка событий", то, возможно, знаете о проверке конечной точки Сетки событий для предотвращения нарушений. CloudEvents версии 1.0 реализует собственную [семантику защиты от нарушений](webhook-event-delivery.md) с помощью метода HTTP OPTIONS. Подробнее об этом можно прочитать [здесь](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). При использовании схемы CloudEvents для вывода служба "Сетка событий" используется с защитой CloudEvents версии 1.0 вместо механизма событий проверки Сетки событий.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Использование с Функциями Azure

[Привязка Сетки событий Функций Azure](../azure-functions/functions-bindings-event-grid.md) не поддерживает CloudEvents в собственном коде, поэтому для чтения сообщений CloudEvents используются функции, активируемые HTTP. При использовании триггера HTTP для чтения CloudEvents необходимо написать код действий, которые триггер службы "Сетка событий" выполняет автоматически:

* Отправляет запрос проверки в [запрос подтверждения подписки](../event-grid/webhook-event-delivery.md).
* Вызывает функцию один раз для каждого элемента массива событий, содержащихся в тексте запроса.

Дополнительные сведения об URL-адресе, используемом для вызова функции, выполняемой локально или в Azure, см. в [справочной документации по привязке триггера HTTP](../azure-functions/functions-bindings-http-webhook.md).

Следующий пример кода C# для триггера HTTP имитирует поведение триггера службы "Сетка событий".  Этот пример можно использовать для событий, доставленных в схеме CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request is not for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Следующий пример кода JavaScript для триггера HTTP имитирует поведение триггера службы "Сетка событий". Этот пример можно использовать для событий, доставленных в схеме CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = { status: 200 };
        context.res.headers.append('Webhook-Allowed-Origin', 'eventgrid.azure.net');
    }
    else
    {
        var message = req.body;
        
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
* Мы рекомендуем вам протестировать CloudEvents и [внести свой вклад](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md) в усовершенствование схемы, оставляя комментарии.
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
