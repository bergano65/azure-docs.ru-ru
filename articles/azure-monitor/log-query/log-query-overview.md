---
title: Анализ данных журнала в Azure Monitor | Документация Майкрософт
description: Чтобы получить данные журнала из Azure Monitor, требуется запрос по журналам.  В этой статье описывается использование новых запросов по журналам в Azure Monitor и приведены основные сведения, которые необходимо знать перед их созданием.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: bwren
ms.openlocfilehash: 1cb3946a93cbeff6a9b95e0a21edbf0523b53d5e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65203613"
---
# <a name="analyze-log-data-in-azure-monitor"></a>Анализ данных журнала в Azure Monitor

Данные журнала, собранные Azure Monitor, хранятся в рабочей области Log Analytics, которая основана на [Azure Data Explorer](/azure/data-explorer). Azure Monitor собирает телеметрию из разных источников и использует [язык запросов Kusto](/azure/kusto/query), применяемый Data Explorer, для извлечения и анализа данных.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="log-queries"></a>Запросы журнала

Чтобы получить данные журнала из Azure Monitor, требуется запрос по журналам.  Чтобы указать нужные данные, используйте запрос, независимо от того, что вы делаете: [анализируете данные на портале](portals.md), [настраиваете правило генерации оповещений](../platform/alerts-metric.md) для уведомления о конкретном состоянии или получаете данные с помощью [API журналов Azure Monitor](https://dev.loganalytics.io/).  В этой статье описываются запросы по журналам в Azure Monitor и предоставляются основные сведения, которые необходимо знать перед их созданием.

## <a name="where-log-queries-are-used"></a>Использование запросов к журналу

Ниже приведены различные способы, что будет использовать запросы в Azure Monitor:

- **Портал.** На [портале Azure](portals.md) можно выполнять интерактивный анализ данных журнала.  Так вы можете изменить запрос и анализировать результаты в различных форматах и визуализациях.  
- **Правила генерации оповещений.** [Правила генерации оповещений](../platform/alerts-overview.md) заранее выявляют проблемы с данными в рабочей области.  Каждое правило генерации оповещений основано на поиске по журналам, который автоматически выполняется через определенные интервалы.  Результаты проверяются, чтобы определить, следует ли создавать оповещение.
- **Панели мониторинга.** Результаты любого запроса можно закрепить на [панели мониторинга Azure](../learn/tutorial-logs-dashboards.md), что даст вам возможность визуализировать данные журналов и метрик вместе и при необходимости использовать совместно с другими пользователями Azure. 
- **Представления.**  Вы можете создавать визуализации данных, которые добавлены на панели мониторинга пользователя, с помощью [конструктора представлений](../platform/view-designer.md).  Запросы к журналам предоставляют данные, используемые [плитками](../platform/view-designer-tiles.md) и [элементами визуализации](../platform/view-designer-parts.md) в каждом просмотре.  

- **Экспорт.**  Когда вы экспортируете данные из Azure Monitor в Excel или [Power BI](../platform/powerbi.md), вы создаете запрос по журналам для определения экспортируемых данных.
- **PowerShell.** Позволяет выполнять скрипт PowerShell из командной строки или модуль runbook службы автоматизации Azure, который использует [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) для получения данных журналов из Azure Monitor.  Для этого командлета требуется запрос, чтобы определить извлекаемые данные.
- **API журналов Azure Monitor.**  [API журналов Azure Monitor](../platform/alerts-overview.md) позволяет любому клиенту REST API извлекать данные журнала из рабочей области.  Запрос API включает запрос, который выполняется в Azure Monitor, чтобы определить извлекаемые данные.

![Поиск по журналам](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>Напишите запрос
Azure Monitor использует [версию языка запросов Kusto](get-started-queries.md) для извлечения и анализа данных журнала различными способами.  Обычно начинают с основных запросов, а затем переходят к использованию более усовершенствованных функций, так как требования становятся более сложными.

Базовая структура запроса — это исходная таблица и ряд операторов, разделенных символом вертикальной черты `|`.  Вы можете объединить в цепочку несколько операторов для уточнения данных и выполнения расширенных функций.

Например, предположим, необходимо найти первые десять компьютеров с наибольшим количеством событий ошибок за последний день.

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

Или нужно найти компьютеры, которые не были активными в течение последнего дня.

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

Как насчет графика, отражающего использование процессора на каждом компьютере за прошлую неделю?

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

Из этих примеров можно увидеть, что независимо от типа данных, с которыми вы работаете, структура запроса является аналогичной.  Ее можно разбить на отдельные действия, где полученные данные из одной команды отправляются через конвейер в следующую команду.

Кроме того, вы можете запрашивать данные из рабочих областей Log Analytics в рамках своей подписки.

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-azure-monitor-log-data-is-organized"></a>Организация данных журнала в Azure Monitor
При создании запроса сначала нужно определить, какие таблицы содержат нужные данные. Различные типы данных разделяются на выделенные таблицы в каждой [рабочей области Log Analytics](../learn/quick-create-workspace.md).  Документация для каждого источника данных включает имя создаваемого типа данных и описание каждого из его свойств.  Многие запросы потребуют только данные из одной таблицы, но другие могут использовать множество параметров для включения данных из нескольких таблиц.

Хотя служба [Application Insights](../app/app-insights-overview.md) хранит данные приложения, например запросы, исключения, трассировки и данные использования в журналах Azure Monitor, эти данные хранятся в отдельном разделе, в отличие от других данных журнала. Используется тот же язык запросов для доступа к этим данным, но для этого должны быть [консоль Application Insights](../app/analytics.md) или [REST API Application Insights](https://dev.applicationinsights.io/). Вы можете использовать [кросс-ресурсные запросы](../log-query/cross-workspace-query.md), чтобы объединять данные Application Insights с другими данными журнала в Azure Monitor.


![Таблицы](media/log-query-overview/queries-tables.png)




## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения об использовании [поисков по журналам Log Analytics для создания и редактирования](../log-query/portals.md).
- Изучите [руководство по написанию запросов](../log-query/get-started-queries.md) на новом языке.
