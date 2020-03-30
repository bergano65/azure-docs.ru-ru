---
title: Схемы диагностических журналов Azure Media Services - Azure
description: В этой статье показаны схемы диагностических журналов мультимедиа Azure Media Services.
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
ms.openlocfilehash: 37baed076ca074c1d558af36649e90959a0034c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750872"
---
# <a name="diagnostic-logs-schemas"></a>Схемы для журналов диагностики

[Azure Monitor](../../azure-monitor/overview.md) позволяет отслеживать метрики и диагностические журналы, которые помогают понять, как работают приложения. Вы можете отслеживать диагностические журналы Media Services и создавать оповещения и уведомления для собранных метрик и журналов. Вы можете отправлять журналы в [хранилище Azure,](https://azure.microsoft.com/services/storage/)передавать их в [концентраторы событий Azure](https://azure.microsoft.com/services/event-hubs/)и экспортировать в [журнал Analytics](https://azure.microsoft.com/services/log-analytics/)или использовать службы третьей стороны.

Для получения подробной информации смотрите [журналы Azure Monitor Metrics](../../azure-monitor/platform/data-platform.md) и [Azure Monitor Diagnostic.](../../azure-monitor/platform/platform-logs-overview.md)

В этой статье описаны схемы диагностических журналов Media Services.

## <a name="top-level-diagnostic-logs-schema"></a>Схема журналов диагностики верхнего уровня

Подробное описание схемы диагностики верхних журналов сверху, [см.](../../azure-monitor/platform/tutorial-dashboards.md)

## <a name="key-delivery-log-schema"></a>Схема журнала доставки ключей

### <a name="properties"></a>Свойства

Эти свойства специфичны для ключевой схемы журнала доставки.

|name|Описание|
|---|---|
|keyId|Идентификатор запрошенного ключа.|
|keyType|Может быть одно из следующих значений: "Ясно" (без шифрования), "FairPlay", "PlayReady", или "Widevine".|
|policyName|Имя менеджера ресурсов Azure.|
|tokenType|Тип токена.|
|statusMessage|Сообщение состояния.|

### <a name="examples"></a>Примеры

Свойства ключевой схемы запросов запросов на доставку.

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

## <a name="additional-notes"></a>Дополнительные замечания

* Widevine — это служба, которая предоставляется компанией Google Inc. и подпадает под условия предоставления услуг и политику конфиденциальности Google Inc.

## <a name="next-steps"></a>Дальнейшие действия

[Мониторинг медиа-услуг метрик и диагностических журналов](media-services-metrics-diagnostic-logs.md)
