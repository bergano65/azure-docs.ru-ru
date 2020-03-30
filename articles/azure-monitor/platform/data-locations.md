---
title: Мониторинг местоположений данных в Azure Monitor Документы Майкрософт
description: Описывает различные места, где хранятся данные мониторинга в Azure, включая платформу данных Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666621"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Мониторинг местоположений данных в Azure Monitor

Azure Monitor основан на [платформе данных](data-platform.md) [журналов](data-platform-logs.md) и [метрик,](data-platform-metrics.md) описанной в [платформе данных Azure Monitor.](data-platform.md) Данные мониторинга из ресурсов Azure могут быть записаны в другие места, либо до их копирования в Azure Monitor, либо для поддержки дополнительных сценариев. 

## <a name="monitoring-data-locations"></a>Мониторинг местоположения данных

В следующей таблице определены различные места, где отправляются данные мониторинга в Azure, и различные методы доступа к ним.

| Расположение | Описание | Варианты доступа |
|:---|:---|:---|:--|
| Метрика azure Monitor | База данных временных рядов, оптимизированная для анализа данных с отметками времени. | [Исследователь метрик](metrics-getting-started.md)<br>[API-извне монитора](/rest/api/monitor/metrics) |
| Журналы Azure Monitor    | Рабочее пространство журнала Analytics, основанное на Azure Data Explorer, который обеспечивает мощный механизм анализа и богатый язык запросов. | [Аналитика журнала](../log-query/portals.md)<br>[API аналитики журнала](https://dev.loganalytics.io/)<br>[API Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Журнал действий | Данные из журнала activity наиболее полезны при отправке в журналы Azure Monitor для анализа других данных, но они также собираются самостоятельно, чтобы их можно было непосредственно просматривать на портале Azure. | [Портал Azure](activity-log-view.md#azure-portal)<br>[API событий Azure Monitor](/rest/api/monitor/eventcategories) |
| Хранилище Azure | Некоторые источники данных будут записываться непосредственно в хранилище Azure и требуют настройки для перемещения данных в журналы. Вы также можете отправлять данные в хранилище Azure для архивирования и интеграции с внешними системами.  | [аналитики хранилища](/rest/api/storageservices/storage-analytics)<br>[Обозреватель серверов](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Обозреватель хранилища](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Центры событий | Отправка данных в концентраторы событий Azure для их потоковой передачи в другие места. | [Захват для хранения](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor для виртуальных машин | Azure Monitor для VMs хранит данные о работе рабочей нагрузки в пользовательском месте, которое используется в опыте мониторинга на портале Azure. | [Портал Azure](../insights/vminsights-overview.md)<br>[Монитор рабочей нагрузки REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[API для работоспособности ресурсов Azure](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| видны узлы | Оповещения, созданные Azure Monitor. | [Портал Azure](alerts-managing-alert-instances.md)<br>[Оповещение управления REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с различными источниками [данных мониторинга, собранными Azure Monitor.](data-sources.md)
- Узнайте о [типах данных мониторинга, собранных Azure Monitor,](data-platform.md) а также о том, как просматривать и анализировать эти данные.
