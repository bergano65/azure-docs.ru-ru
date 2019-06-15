---
title: Отслеживание метрик службы мультимедиа и журналов диагностики с помощью Azure Monitor | Документация Майкрософт
description: В этой статье описаны способы мониторинга службы мультимедиа метрик и журналов диагностики с помощью Azure Monitor.
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
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: bbf43ecb07947fad8cc1ee064d2038e4a21d4444
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964770"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Отслеживание метрик службы мультимедиа и журналы диагностики

[Azure Monitor](../../azure-monitor/overview.md) включает мониторинг метрик и журналов диагностики, которые помогут вам понять, как выполняемых приложений. Все данные, собранные Azure Monitor, помещаются в одну из двух основных типов, метрики и журналы. Можно отслеживать журналы диагностики служб мультимедиа и создавать оповещения и уведомления для сбора метрик и журналов. Вы можете визуализировать и анализировать данные метрик с помощью [обозревателя метрик](../../azure-monitor/platform/metrics-getting-started.md). Вы можете отправить журналы в [хранилища Azure](https://azure.microsoft.com/services/storage/), потоковая передача журналов в [концентраторов событий](https://azure.microsoft.com/services/event-hubs/)и экспортировать их в службу [Log Analytics](https://azure.microsoft.com/services/log-analytics/), или использовать сторонние службы.

Подробный обзор см. в разделе [метрик Azure Monitor](../../azure-monitor/platform/data-platform.md) и [журналов диагностики Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

В этом разделе обсуждаются в настоящее время доступна [метрики службы носителя](#media-services-metrics) и [журналов диагностики службы мультимедиа](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Метрики службы мультимедиа

Метрики собираются через регулярные промежутки времени независимо от того, изменяется ли значение. Метрики удобно использовать для создания оповещений, так можно часто делать их выборку и быстро создавать оповещения с использованием относительно простой логики.

В настоящее время следующие службы мультимедиа [конечными точками потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingendpoints) метрики передаются в Azure:

|Метрика|`Display name`|Описание|
|---|---|---|
|Requests|Requests|Предоставляет сведения, общее число запросов, обслуживаемых конечной точки потоковой передачи.|
|Исходящие|Исходящие|Общее число исходящих байтов. Например в потоке с конечной точки потоковой передачи байтов.|
|SuccessE2ELatency|Задержка успешных сквозного| Сведения о сквозная задержка успешных запросов.|

Например, чтобы получить метрики «Исходящий» с помощью интерфейса командной строки, необходимо выполнить следующие `az monitor metrics` команду интерфейса командной строки:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Сведения о том, как создать оповещения о метриках см. в разделе [создание, просмотр и управление оповещения метрик с помощью Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>Журналы диагностики служб мультимедиа

В настоящее время можно получить следующие журналы диагностики:

|ИМЯ|Описание|
|---|---|
|Запрос службы доставки ключей|Журналы, которые показывают сведения о запросе службы доставки ключей. Дополнительные сведения см. в разделе [схемы](media-services-diagnostic-logs-schema.md).|

Чтобы включить журналов диагностики в учетную запись хранения, необходимо выполнить следующие `az monitor diagnostic-settings` команду интерфейса командной строки: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Пример:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>Дальнейшие действия 

[Как собирать и использовать данные журнала из ресурсов Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).
