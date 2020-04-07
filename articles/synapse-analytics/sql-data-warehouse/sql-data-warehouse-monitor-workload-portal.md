---
title: Мониторинг рабочей нагрузки - Портал Azure
description: Мониторинг Синапсе СЗЛ с помощью портала Azure
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 0658a775e40c1fc433c7c2e1d853493544e74ee4
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743203"
---
# <a name="monitor-workload---azure-portal"></a>Мониторинг рабочей нагрузки - Портал Azure

В этой статье описывается, как использовать портал Azure для мониторинга рабочей нагрузки. Это включает в себя настройку журналов Azure Monitor для изучения тенденций выполнения запросов и рабочей нагрузки с помощью аналитики журналов для [Synapse S'L.](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
- Бассейн S'L: Мы будем собирать журналы для пула S'L. Если у вас нет подготовленного пула S'L, [см.](load-data-from-azure-blob-storage-using-polybase.md)

## <a name="create-a-log-analytics-workspace"></a>Создание рабочей области Log Analytics

Перейдите к лезвию просмотра для рабочих областей log Analytics и создайте рабочее пространство

![Рабочие области Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Добавление рабочего пространства Analytics](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Добавление рабочего пространства Analytics](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Для получения более подробной информации о рабочих областях, посетите следующую [документацию](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Включите диагностические журналы

Настройте диагностические настройки для испускаемых журналов из пула S'L. Логи состоят из телеметрических представлений, эквивалентных наиболее часто используемым Дим-исправлениям производительности. В настоящее время поддерживаются следующие мнения:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Включение журналов диагностики](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Записи могут быть излмиссии в хранилище Azure, stream Analytics или Log Analytics. Для этого руководства выберите Log Analytics.

![Указать журналы](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Запуск запросов в отношении аналитики журналов

Перейдите в рабочее пространство Log Analytics, где вы можете сделать следующее:

- Анализ журналов с помощью журналов и сохранение запросов для повторного использования
- Сохранение запросов для повторного использования
- Создание оповещений журнала
- Результаты запроса на панели мониторинга

Для получения подробной информации о возможностях журнальных запросов посетите следующую [документацию.](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

![Редактор рабочего пространства журнала Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Запросы рабочего пространства журналов Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Запросы образцов журналов

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

Теперь, когда вы настроили и настроили журналы мониторов Azure, [настройте панели мониторинга Azure](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) для обмена данными в вашей команде.
