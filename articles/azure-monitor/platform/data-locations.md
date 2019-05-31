---
title: Мониторинг расположения данных в Azure Monitor | Документация Майкрософт
description: Описание разных местах, где хранятся данные мониторинга в Azure, включая платформы данных Azure Monitor.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 1d92973e32e9c694b1d0488753b9a701e7d71a5d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416916"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Мониторинг расположения данных в Azure Monitor

На основе Azure Monitor [платформа данных](data-platform.md) из [журналы](data-platform-logs.md) и [метрики](data-platform-metrics.md) как описано в разделе [платформы данных Azure Monitor](data-platform.md). Данные мониторинга из ресурсов Azure могут записываться в другие расположения, либо перед их копированием в Azure Monitor или для поддержки дополнительных сценариев. 

## <a name="monitoring-data-locations"></a>Мониторинг данных расположения

Ниже приведены различные расположения, куда отправляются данных мониторинга в Azure и различные методы для доступа к нему.

| Расположение | Описание | Варианты доступа |
|:---|:---|:---|:--|
| Метрики Azure Monitor | База данных временных рядов, оптимизированный для анализа данных с меткой времени. | [Обозреватель метрик](metrics-getting-started.md)<br>[API метрик Azure Monitor](/rest/api/monitor/metrics) |
| Журналы Azure Monitor    | Рабочая область log Analytics, основанный на обозреватель данных Azure, который предоставляет мощные функции анализа ядра и обширные возможности язык запросов. | [Служба Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[API Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Журнал действий | Данные из журнала действий наиболее полезен при отправке журналов Azure Monitor для их анализа с другими данными, но также собираются сам по себе, чтобы можно было напрямую просматривать на портале Azure. | [портал Azure](activity-log-view.md#azure-portal)<br>[API событий Azure Monitor](/rest/api/monitor/eventcategories) |
| Хранилище Azure | Некоторые источники данных будет запись напрямую в хранилище Azure и требует настройки, чтобы переместить данные в журналы. Вы также можете отправлять данные в службу хранилища Azure для архивирования, а также для интеграции с внешними системами.  | [Аналитика службы хранилища](/rest/api/storageservices/storage-analytics)<br>[Обозреватель серверов](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Обозреватель хранилища](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Центры событий; | Отправьте данные в концентраторы событий Azure потоковой передачи в другие расположения. | [Запись в хранилище](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor для виртуальных машин | Azure Monitor для виртуальных машин хранит данные о работоспособности рабочей нагрузки в настраиваемом расположении, используемый работы мониторинга на портале Azure. | [портал Azure](../insights/vminsights-overview.md)<br>[REST API monitor рабочей нагрузки](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure REST API работоспособности ресурсов](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Оповещения | Оповещения, созданные этим Azure Monitor. | [портал Azure](alerts-managing-alert-instances.md)<br>[REST API Управление оповещениями](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Дальнейшие действия

- См. в разделе различные источники [данные мониторинга собранные Azure Monitor](data-sources.md).
- Дополнительные сведения о [типы данных мониторинга, собранные Azure Monitor](data-platform.md) и как просматривать и анализировать эти данные.
