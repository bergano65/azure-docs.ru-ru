---
title: Использование службы "Сетка событий Azure" с событиями в схеме CloudEvents
description: В этой статье описана установка схемы CloudEvents для событий в службе "Сетка событий Azure".
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: 8925110511f6c63a42dd9b121429ac7264cd4aa4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170249"
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

Подробное описание доступных полей, их типов и определений в Клаудевентс v 0,1 [можно найти здесь](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Значения заголовков для событий, доставляемых в схеме CloudEvents и схеме "Сетка событий", совпадают, за исключением элемента `content-type`. Для схемы CloudEvents значение заголовка — `"content-type":"application/cloudevents+json; charset=utf-8"`. Для схемы "Сетка событий" значение заголовка — `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Настройка сетки событий для CloudEvents

Службу "Сетка событий" можно использовать для ввода и вывода событий в схеме CloudEvents. Кроме того, CloudEvents можно использовать для системных событий, таких как события хранилища BLOB-объектов и Центра Интернета вещей, а также настраиваемые события. CloudEvents также может преобразовать эти события (в другой формат и обратно) при передаче по сети.


| Схема ввода       | Схема вывода
|--------------------|---------------------
| Формат CloudEvents | Формат CloudEvents
| Формат сетки событий  | Формат CloudEvents
| Формат CloudEvents | Формат сетки событий
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
  --input-schema cloudeventv01schema
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
  -InputSchema CloudEventV01Schema
```

Текущая версия CloudEvents не поддерживает пакетную обработку событий. При публикации в разделе с помощью схемы CloudEvent необходимо публиковать каждое событие отдельно.

### <a name="output-schema"></a>Схема вывода

Схема вывода указывается при создании подписки на событие.

Для интерфейса командной строки Azure:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Для PowerShell используйте команду:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

 Сейчас, если событие доставляется в схему CloudEvents, триггер службы "Сетка событий" нельзя использовать в приложении "Функции Azure". Используйте триггер HTTP. Примеры реализации триггера HTTP, который получает события в схеме CloudEvents, см. в разделе [Использование триггера HTTP в качестве триггера службы "Сетка событий"](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со сведениями о [мониторинге доставки сообщений в службе "Сетка событий"](monitor-event-delivery.md).
* Мы рекомендуем вам протестировать CloudEvents и [внести свой вклад](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) в усовершенствование схемы, оставляя комментарии.
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
