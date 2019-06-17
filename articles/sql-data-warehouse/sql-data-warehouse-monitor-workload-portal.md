---
title: Монитор рабочей нагрузки — портал Azure | Документация Майкрософт
description: Монитор хранилища данных SQL Azure с помощью портала Azure
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60748907"
---
# <a name="monitor-workload---azure-portal"></a>Монитор рабочей нагрузки — портал Azure

В этой статье описывается мониторинг рабочей нагрузки с помощью портала Azure. Сюда входит Настройка журналов Azure Monitor для выполнения и рабочей нагрузки тенденции запросов с помощью log analytics для анализа [хранилище данных SQL Azure](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Технические условия

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
- Хранилище данных SQL Azure. Мы будет собирать журналы для хранилища данных SQL. Если у вас нет подготовки хранилища данных SQL, см. инструкции в [Создание хранилища данных SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Создание рабочей области Log Analytics

Перейдите к колонке "Обзор" для рабочих областей Log Analytics и создайте рабочую область 

![Рабочие области Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Добавление рабочей области Analytics](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Добавление рабочей области Analytics](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Дополнительные сведения о рабочих областях, посетите следующий [документации](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Включение журналов диагностики 

Настройка параметров диагностики для передачи журналов из хранилища данных SQL. Журналы состоят из представлений телеметрии эквивалентно наиболее часто используемые производительности, динамических административных представлений для устранения неполадок в хранилище данных SQL хранилища данных. В настоящее время поддерживаются следующие представления:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Включение журналов диагностики](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Журналы могут выдаваться для службы хранилища Azure Stream Analytics и Log Analytics. Для этого руководства выберите Log Analytics.

![Укажите журналы](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Выполнение запросов к Log Analytics

Перейдите в рабочую область Log Analytics, где можно сделать следующее:

- Анализ журналов с помощью запросов к журналу и сохранять запросы для повторного использования
- Сохранять запросы для повторного использования
- Создание оповещений журнала
- Результаты запроса ПИН-кода на панели мониторинга

Дополнительные сведения о возможности запросов к журналу, посетите следующий [документации](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Редактор рабочей области log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Журнал запросов Analytics рабочей области](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Пример журнала запросов



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

Теперь, когда вы настроили и настроить журналы Azure monitor, [Настройка панелей мониторинга Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) для совместного использования рабочей группы.
