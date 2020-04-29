---
title: Наблюдение за расположением данных в Azure Monitor | Документация Майкрософт
description: Описание различных расположений, в которых данные мониторинга хранятся в Azure, включая платформу данных Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77666621"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Наблюдение за расположением данных в Azure Monitor

Azure Monitor основан на [платформе данных](data-platform.md) [журналов](data-platform-logs.md) и [метриках](data-platform-metrics.md) , как описано в статье [Azure Monitor Data Platform](data-platform.md). Данные мониторинга из ресурсов Azure могут быть записаны в другие расположения, прежде чем они будут скопированы в Azure Monitor или поддерживать дополнительные сценарии. 

## <a name="monitoring-data-locations"></a>Мониторинг расположения данных

В следующей таблице указаны различные расположения, в которых отправляются данные мониторинга в Azure, и различные методы доступа к ним.

| Местоположение | Описание | Варианты доступа |
|:---|:---|:---|:--|
| Метрики Azure Monitor | База данных временных рядов, оптимизированная для анализа данных с отметками времени. | [обозреватель метрик](metrics-getting-started.md)<br>[API метрик Azure Monitor](/rest/api/monitor/metrics) |
| Журналы Azure Monitor    | Log Analytics рабочей области, основанной на обозреватель данных Azure, которая предоставляет мощный модуль анализа и расширенный язык запросов. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[API Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Журнал действий | Данные из журнала действий наиболее полезны при отправке в журналы Azure Monitor для анализа с другими данными, но они также собираются самостоятельно, чтобы их можно было просматривать непосредственно в портал Azure. | [Портал Azure](activity-log-view.md#azure-portal)<br>[API событий Azure Monitor](/rest/api/monitor/eventcategories) |
| Хранилище Azure | Некоторые источники данных будут записывать данные непосредственно в службу хранилища Azure и требовать настройки для перемещения данных в журналы. Вы также можете отправить данные в службу хранилища Azure для архивирования и интеграции с внешними системами.  | [Аналитика Службы хранилища](/rest/api/storageservices/storage-analytics)<br>[Обозреватель серверов](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Обозреватель службы хранилища](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Центры событий | Отправка данных в концентраторы событий Azure для потоковой передачи в другие расположения. | [Запись в хранилище](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor для виртуальных машин | Azure Monitor для виртуальных машин сохраняет данные о работоспособности рабочей нагрузки в пользовательском расположении, которое используется для мониторинга в портал Azure. | [Портал Azure](../insights/vminsights-overview.md)<br>[Монитор рабочей нагрузки REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[REST API работоспособности ресурсов Azure](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Предупреждения | Предупреждения, созданные Azure Monitor. | [Портал Azure](alerts-managing-alert-instances.md)<br>[REST API управления оповещениями](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Дальнейшие шаги

- Ознакомьтесь с различными источниками [данных мониторинга, собранными Azure Monitor](data-sources.md).
- Сведения о [типах данных мониторинга, собираемых Azure Monitor](data-platform.md) , а также о том, как просматривать и анализировать эти данные.
