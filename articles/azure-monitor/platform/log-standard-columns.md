---
title: Стандартные столбцы в Azure Monitor записей журнала | Документация Майкрософт
description: Описывает столбцы, общие для нескольких типов данных в журналах Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: dc3d119479d2dce45b286463f3d6a76410220dd0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014226"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Стандартные столбцы в журналах Azure Monitor
Данные в журналах Azure Monitor [хранятся в виде набора записей в log Analytics рабочей области или в Application Insights приложении](./data-platform-logs.md), каждый из которых имеет определенный тип данных, имеющий уникальный набор столбцов. Многие типы данных будут иметь стандартные столбцы, которые являются общими для нескольких типов. В этой статье описываются эти столбцы и приводятся примеры их использования в запросах.

Приложения на основе рабочей области в Application Insights хранят свои данные в Log Analytics рабочей области и используют те же стандартные столбцы, что и другие таблицы в рабочей области. Классические приложения хранят свои данные отдельно и имеют различные стандартные столбцы, как указано в этой статье.

> [!NOTE]
> Некоторые стандартные столбцы не отображаются в представлении схемы или IntelliSense в Log Analytics и не отображаются в результатах запроса, если только столбец в выходных данных не указан явным образом.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated и timestamp
Столбцы **TimeGenerated** log Analytics (Рабочая область) и **метка времени** (Application Insights приложение) содержат дату и время создания записи источником данных. Дополнительные сведения см. [в разделе время приема данных журнала в Azure Monitor](data-ingestion-time.md) .

**Timegenerated** и **timestamp** предоставляют общий столбец, используемый для фильтрации или суммирования по времени. При выборе диапазона времени для представления или панели мониторинга в портал Azure для фильтрации результатов используется TimeGenerated или метка времени. 

### <a name="examples"></a>Примеры

Указанный ниже запрос возвращает количество событий с ошибками за каждый день на прошлой неделе.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

Следующий запрос возвращает количество исключений, созданных для каждого дня предыдущей недели.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_тимерецеивед
Столбец **\_ тимерецеивед** содержит дату и время получения записи Azure Monitor точкой приема в облаке Azure. Это может быть полезно для выявления проблем задержки между источником данных и облаком. Примером может быть проблема с сетью, вызывающая задержку данных, отправляемых агентом. Дополнительные сведения см. [в разделе время приема данных журнала в Azure Monitor](data-ingestion-time.md) .

Следующий запрос возвращает среднюю задержку на час для записей событий агента. Сюда входит время из агента в облако и общее время, в течение которого запись будет доступна для запросов журнала.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Тип и itemType
Столбцы **тип** (log Analytics Рабочая область) и **ItemType** (Application Insights Application) содержат имя таблицы, из которой была получена запись, которая также может рассматриваться как тип записи. Этот столбец полезен в запросах, объединяющих записи из нескольких таблиц, например те, которые используют `search` оператор, для различения записей различных типов. В некоторых случаях вместо **Type** можно использовать **$table**.

### <a name="examples"></a>Примеры
Указанный ниже запрос возвращает количество записей по типу, собранных за последний час.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
Столбец **\_ ItemId** содержит уникальный идентификатор записи.


## <a name="_resourceid"></a>\_ResourceId
Столбец **\_ ResourceId** содержит уникальный идентификатор ресурса, с которым связана запись. Это позволяет использовать стандартный столбец для определения области запроса только для записей из определенного ресурса или для объединения связанных данных между несколькими таблицами.

Для ресурсов Azure значением параметра **_ResourceId** будет [URL-адрес идентификатора ресурса Azure](../../azure-resource-manager/templates/template-functions-resource.md). В настоящее время столбец ограничен ресурсами Azure, но он будет расширен для ресурсов, находящихся за пределами Azure, например с локальных компьютеров.

> [!NOTE]
> Некоторые типы данных уже содержат поля с идентификатором ресурса Azure или его частями, например идентификатором подписки. Хотя эти поля нужны для обеспечения обратной совместимости, рекомендуем использовать свойство _ResourceId для перекрестной корреляции, так как в этом случае она будет более согласованной.

### <a name="examples"></a>Примеры
Указанный ниже запрос объединяет данные о производительности и событиях для каждого компьютера. В результатах показаны все события с идентификатором _101_ и загрузкой процессора больше 50 %.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Указанный ниже запрос объединяет записи _AzureActivity_ и записи _SecurityEvent_. В результатах показаны все операции с пользователями, которые вошли на эти виртуальные машины.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

Следующий запрос анализирует **_ResourceId** и суммирует объемы данных, выставленных в подписке Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Используйте эти запросы `union withsource = tt *` только в случае необходимости, так как сканирование по типам данных требует больших затрат на выполнение.

## <a name="_isbillable"></a>\_IsBillable
Столбец «ведомость» указывает, **\_ оплачивается** ли прием данных. Данные со **\_ счетами** , равными `false` , собираются бесплатно и не выставляются в учетную запись Azure.

### <a name="examples"></a>Примеры
Чтобы получить список компьютеров, отправляющие счет за типы данных, используйте следующий запрос.

> [!NOTE]
> Используйте запросы `union withsource = tt *` только в случае необходимости, так как сканирование по типам данных требует больших затрат на выполнение. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Это может быть расширено, чтобы возвращать количество компьютеров в час, которые отправляют счета за типы данных.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
Столбец **\_ билледсизе** указывает размер данных в байтах, которые будут оплачиваться в учетную запись Azure, если **\_ оплачивается** .


### <a name="examples"></a>Примеры
Чтобы просмотреть размер оплачиваемых событий, принимаемых на компьютер, используйте `_BilledSize` столбец, который предоставляет размер в байтах:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Чтобы просмотреть размер оплачиваемых событий, принимаемых на подписку, используйте следующий запрос:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Чтобы просмотреть размер оплачиваемых событий, принимаемых на группу ресурсов, используйте следующий запрос:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Чтобы узнать количество полученных событий для каждого компьютера, выполните следующий запрос.

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Чтобы узнать количество полученных оплачиваемых событий для каждого компьютера, выполните следующий запрос. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Чтобы просмотреть число оплачиваемых типов данных с определенного компьютера, используйте следующий запрос:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. в статье [Анализ данных журнала в Azure Monitor](../log-query/log-query-overview.md).
- Изучите статью [Начало работы с запросами журналов Azure Monitor](../log-query/get-started-queries.md).
- См. статью [Объединения в запросах журнала Azure Monitor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).