---
title: Службы мультимедиа Azure диагностические журналы схемы — Azure
description: В этой статье показано схемы журналов диагностики служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 394370738bc7996a221300540e68404986d91310
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850627"
---
# <a name="diagnostic-logs-schemas"></a>Схемы для журналов диагностики

[Azure Monitor](../../azure-monitor/overview.md) включает мониторинг метрик и журналов диагностики, которые помогут вам понять, как выполняемых приложений. Можно отслеживать журналы диагностики служб мультимедиа и создавать оповещения и уведомления для сбора метрик и журналов. Вы можете отправить журналы в [хранилища Azure](https://azure.microsoft.com/services/storage/), потоковая передача журналов в [концентраторов событий](https://azure.microsoft.com/services/event-hubs/)и экспортировать их в службу [Log Analytics](https://azure.microsoft.com/services/log-analytics/), или использовать сторонние службы.

Подробные сведения см. в разделе [метрик Azure Monitor](../../azure-monitor/platform/data-platform.md) и [журналов диагностики Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

В этой статье описывается схемы журналов диагностики службы мультимедиа.

## <a name="top-level-diagnostic-logs-schema"></a>Схема журналов диагностики верхнего уровня

Подробное описание схемы верхнего уровня журналы диагностики, см. в разделе [поддерживаемые службы, схемы и категории для журналов диагностики Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Схема журнала доставки ключей

### <a name="properties"></a>properties

Эти свойства относятся к схеме журнала доставки ключей.

|ИМЯ|ОПИСАНИЕ|
|---|---|
|Идентификатор ключа|Идентификатор запрошенного ключа.|
|keyType|Может принимать одно из следующих значений: «Очистить» (без шифрования), «FairPlay», «PlayReady» или «Widevine».|
|policyName|Имя политики Azure Resource Manager.|
|TokenType|Тип маркера.|
|Сообщение состояния|Сообщение о состоянии.|

### <a name="examples"></a>Примеры

Свойства схемы запросы доставки ключей.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>Дальнейшие действия

[Отслеживание метрик службы мультимедиа и журналы диагностики](media-services-metrics-diagnostic-logs.md)
