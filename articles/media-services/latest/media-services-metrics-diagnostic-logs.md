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
ms.openlocfilehash: 4252b0f26ef3c02216a41dde5d2e1cb5ea0efd6a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556396"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Отслеживание метрик службы мультимедиа и журналы диагностики

[Azure Monitor](../../azure-monitor/overview.md) включает мониторинг метрик и журналов диагностики, которые помогут вам понять, как выполняемых приложений. Все данные, собранные Azure Monitor, помещаются в одну из двух основных типов, метрик и журналов. Можно отслеживать журналы диагностики служб мультимедиа и создавать оповещения и уведомления для сбора метрик и журналов. Вы можете визуализировать и анализировать данные метрик с помощью [обозревателя метрик](../../azure-monitor/platform/metrics-getting-started.md). Вы можете отправить журналы в [хранилища Azure](https://azure.microsoft.com/services/storage/), потоковая передача журналов в [концентраторов событий](https://azure.microsoft.com/services/event-hubs/)и экспортировать их в службу [Log Analytics](https://azure.microsoft.com/services/log-analytics/), или использовать сторонние службы.

Подробный обзор см. в разделе [метрик Azure Monitor](../../azure-monitor/platform/data-collection.md) и [журналов диагностики Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

В этом разделе обсуждаются в настоящее время доступна [метрики службы носителя](#media-services-metrics) и [журналов диагностики службы мультимедиа](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Метрики службы мультимедиа

Метрики собираются через регулярные промежутки времени независимо от того, изменяется ли значение. Метрики удобно использовать для создания оповещений, так можно часто делать их выборку и быстро создавать оповещения с использованием относительно простой логики.

В настоящее время следующие службы мультимедиа [конечными точками потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingendpoints) метрики передаются в Azure:

|ИМЯ|ОПИСАНИЕ|
|---|---|
|Requests|Предоставляет сведения, общее число запросов, обслуживаемых конечной точки потоковой передачи.|
|Исходящие|Общее число исходящих байтов. Например выполняют потоковую передачу конечная точка потоковой передачи байтов.|
|Задержка успешных сквозного| Сведения о сквозная задержка успешных запросов.|

Например, чтобы получить метрики «Исходящий» с помощью интерфейса командной строки, необходимо выполнить следующие `az monitor metrics` команду интерфейса командной строки:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Сведения о том, как создать оповещения о метриках см. в разделе [создание, просмотр и управление оповещения метрик с помощью Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>Журналы диагностики служб мультимедиа

В настоящее время можно получить следующие журналы диагностики:

|ИМЯ|ОПИСАНИЕ|
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

Например: 

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>Дальнейшие действия 

[Как собирать и использовать данные журнала из ресурсов Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).
