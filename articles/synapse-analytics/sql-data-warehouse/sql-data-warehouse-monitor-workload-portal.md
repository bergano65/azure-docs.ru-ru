---
title: Мониторинг рабочей нагрузки — портал Azure
description: Мониторинг SQL синапсе с помощью портал Azure
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 327174974affb3b2511eac60755aa1bf047b3b5e
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133467"
---
# <a name="monitor-workload---azure-portal"></a>Мониторинг рабочей нагрузки — портал Azure

В этой статье описывается, как использовать портал Azure для мониторинга рабочей нагрузки. Сюда входит настройка журналов Azure Monitor для изучения тенденций выполнения запросов и рабочих нагрузок с помощью log Analytics для [СИНАПСЕ SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Предварительные условия

- Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) , прежде чем начинать работу.
- Пул SQL. Мы будем собирать журналы для пула SQL. Если у вас нет подготовленного пула SQL, см. инструкции в разделе [Создание пула SQL](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="create-a-log-analytics-workspace"></a>Создание рабочей области Log Analytics

Перейдите в колонку "Обзор" для Log Analytics рабочих областей и создайте рабочую область.

![Рабочие области Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Добавить рабочую область аналитики](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Добавить рабочую область аналитики](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Дополнительные сведения о рабочих областях см. в следующей [документации](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace).

## <a name="turn-on-resource-logs"></a>Включить журналы ресурсов

Настройте параметры диагностики, чтобы выдать журналы из пула SQL. Журналы состоят из представлений телеметрии, эквивалентных наиболее часто используемым DMV диагностики производительности. В настоящее время поддерживаются следующие представления:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Включение журналов ресурсов](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Журналы можно выдавать в службу хранилища Azure, Stream Analytics или Log Analytics. Для работы с этим руководством выберите Log Analytics.

![Указание журналов](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Выполнение запросов к Log Analytics

Перейдите в рабочую область Log Analytics, в которой можно выполнить следующие действия.

- Анализ журналов с помощью запросов журналов и сохранение запросов для повторного использования
- Сохранение запросов для повторного использования
- Создание оповещений журнала
- Закрепление результатов запроса на панели мониторинга

Дополнительные сведения о возможностях запросов журналов см. в следующей [документации](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

![Редактор рабочей области Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Запросы рабочей области Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

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

## <a name="next-steps"></a>Следующие шаги

Теперь, после настройки и настройки журналов Azure Monitor, [Настройте панели мониторинга Azure](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) для совместного использования в команде.
