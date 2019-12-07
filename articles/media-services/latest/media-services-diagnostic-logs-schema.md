---
title: Схемы журналов диагностики служб мультимедиа Azure — Azure
description: В этой статье описываются схемы журналов диагностики служб мультимедиа Azure.
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
ms.openlocfilehash: 2f5fcf4d9106bf37bcc81388e48afe689f4ef4d6
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896049"
---
# <a name="diagnostic-logs-schemas"></a>Схемы для журналов диагностики

[Azure Monitor](../../azure-monitor/overview.md) позволяет отслеживать метрики и журналы диагностики, которые помогут понять, как работают приложения. Вы можете отслеживать журналы диагностики служб мультимедиа и создавать оповещения и уведомления для собранных метрик и журналов. Вы можете отправить журналы в службу [хранилища Azure](https://azure.microsoft.com/services/storage/), выполнить их потоковую передачу в [концентраторы событий Azure](https://azure.microsoft.com/services/event-hubs/)и экспортировать их в [log Analytics](https://azure.microsoft.com/services/log-analytics/)или использовать сторонние службы.

Подробные сведения см. в разделе [метрики Azure Monitor](../../azure-monitor/platform/data-platform.md) и [Azure Monitor журналов диагностики](../../azure-monitor/platform/resource-logs-overview.md).

В этой статье описываются схемы журналов диагностики служб мультимедиа.

## <a name="top-level-diagnostic-logs-schema"></a>Схема журналов диагностики верхнего уровня

Подробное описание схемы журналов диагностики верхнего уровня см. в статье [Поддерживаемые службы, схемы и категории для журналов диагностики Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Схема журнала доставки ключей

### <a name="properties"></a>properties

Эти свойства относятся к схеме журнала доставки ключей.

|Name|Описание|
|---|---|
|keyId|ИДЕНТИФИКАТОР запрошенного ключа.|
|keyType|Может принимать одно из следующих значений: "Clear" (без шифрования), "FairPlay", "PlayReady" или "Widevine".|
|policyName|Azure Resource Manager имя политики.|
|tokenType|Тип маркера.|
|statusMessage|Сообщение о состоянии.|

### <a name="examples"></a>Примеры

Свойства схемы запросов на доставку ключей.

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

[Мониторинг метрик и журналов диагностики служб мультимедиа](media-services-metrics-diagnostic-logs.md)
