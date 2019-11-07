---
title: Мониторинг рабочей нагрузки — портал Azure
description: Мониторинг хранилища данных SQL Azure с помощью портал Azure
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 73d837c34dd5a480cae08a41c89939414899052a
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645634"
---
# <a name="monitor-workload---azure-portal"></a>Мониторинг рабочей нагрузки — портал Azure

В этой статье описывается, как использовать портал Azure для мониторинга рабочей нагрузки. Сюда входит настройка журналов Azure Monitor для изучения тенденций выполнения запросов и рабочих нагрузок с помощью log Analytics для [хранилища данных SQL Azure](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
- Хранилище данных SQL Azure. Мы будем собирать журналы для хранилища данных SQL. Если у вас нет подготовленного хранилища данных SQL, см. инструкции в разделе [Создание хранилища данных SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Создание рабочей области Log Analytics

Перейдите в колонку "Обзор" для Log Analytics рабочих областей и создайте рабочую область. 

![Рабочие области Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Добавить рабочую область аналитики](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Добавить рабочую область аналитики](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Дополнительные сведения о рабочих областях см. в следующей [документации](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Включить журналы диагностики 

Настройте параметры диагностики, чтобы выдать журналы из хранилища данных SQL. Журналы состоят из представлений телеметрии хранилища данных, эквивалентных наиболее часто используемым представлениям диагностики производительности для хранилища данных SQL. В настоящее время поддерживаются следующие представления:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Включение журналов диагностики](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Журналы можно выдавать в службу хранилища Azure, Stream Analytics или Log Analytics. Для работы с этим руководством выберите Log Analytics.

![Указание журналов](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Выполнение запросов к Log Analytics

Перейдите в рабочую область Log Analytics, в которой можно выполнить следующие действия.

- Анализ журналов с помощью запросов журналов и сохранение запросов для повторного использования
- Сохранение запросов для повторного использования
- Создание оповещений журнала
- Закрепление результатов запроса на панели мониторинга

Дополнительные сведения о возможностях запросов журналов см. в следующей [документации](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Редактор рабочей области Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Запросы рабочей области Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Примеры запросов журналов



```Kusto
//List all queries 
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```
```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart 
```
```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits" 
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```
## <a name="next-steps"></a>Дальнейшие действия

Теперь, после настройки и настройки журналов Azure Monitor, [Настройте панели мониторинга Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) для совместного использования в команде.
