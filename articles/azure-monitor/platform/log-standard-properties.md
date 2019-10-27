---
title: Стандартные свойства в записях журнала Azure Monitor | Документация Майкрософт
description: Описание свойств, общих для многих типов данных в журналах Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: d765422957392a5cdb170208b809c24bf5aec2a3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932194"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Стандартные свойства в журналах Azure Monitor
Данные в журналах Azure Monitor [хранятся в виде набора записей в log Analytics рабочей области или в Application Insights приложении](../log-query/logs-structure.md), каждый из которых имеет определенный тип данных, имеющий уникальный набор свойств. Большинство типов данных имеют стандартные свойства, которые являются общими для нескольких типов. В этой статье описаны эти свойства и приведены примеры по их использованию в запросах.

> [!NOTE]
> Некоторые стандартные значения по мере использования не отображаются в представлении схемы или IntelliSense в Log Analytics и не отображаются в результатах запроса, если только в выходных данных не указано явное свойство.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated и timestamp
Свойства **TimeGenerated** log Analytics (Рабочая область) и **timestamp** (Application Insights приложение) содержат дату и время создания записи источником данных. Дополнительные сведения см. [в разделе время приема данных журнала в Azure Monitor](data-ingestion-time.md) .

**Timegenerated** и **timestamp** предоставляют общее свойство, используемое для фильтрации или суммирования по времени. При выборе диапазона времени для представления или панели мониторинга в портал Azure для фильтрации результатов используется TimeGenerated или метка времени. 

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

## <a name="_timereceived"></a>\_Тимерецеивед
Свойство **\_тимерецеивед** содержит дату и время получения записи Azure Monitor точкой приема в облаке Azure. Это может быть полезно для выявления проблем задержки между источником данных и облаком. Примером может быть проблема с сетью, вызывающая задержку данных, отправляемых агентом. Дополнительные сведения см. [в разделе время приема данных журнала в Azure Monitor](data-ingestion-time.md) .

Следующий запрос возвращает среднюю задержку на час для записей событий агента. Сюда входит время от агента до облака и общее время, в течение которого запись будет доступна для запросов журнала.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Тип и itemType
Свойства **тип** (log Analytics Рабочая область) и **ItemType** (Application Insights Application) содержат имя таблицы, из которой была получена запись, которая также может рассматриваться как тип записи. Это свойство можно использовать в запросах, где объединяются записи из нескольких таблиц, например использующих оператор `search`, чтобы различать записи разных типов. В некоторых случаях вместо **Type** можно использовать **$table**.

### <a name="examples"></a>Примеры
Указанный ниже запрос возвращает количество записей по типу, собранных за последний час.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
Свойство **\_ItemId** содержит уникальный идентификатор записи.


## <a name="_resourceid"></a>\_ResourceId
Свойство **\_ResourceId** содержит уникальный идентификатор для ресурса, с которым связана запись. Это стандартное свойство можно использовать, чтобы выполнить запрос только к записям из определенного ресурса или объединить связанные данные из нескольких таблиц.

Для ресурсов Azure значением параметра **_ResourceId** будет [URL-адрес идентификатора ресурса Azure](../../azure-resource-manager/resource-group-template-functions-resource.md). Сейчас это свойство поддерживается только для ресурсов Azure, но в будущем оно будет доступно и для ресурсов за пределами Azure, например локальных компьютеров.

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
Свойство **\_IsBillable** указывает, являются ли входящие данные оплачиваемыми. Данные, которые имеют свойство **\_IsBillable** равно значению _false_, собираются бесплатно и не оплачиваются в вашей учетной записи Azure.

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
Свойство **\_BilledSize** определяет размер данных в байтах, которые будут оплачиваться в вашей учетной записи Azure, если свойство **\_IsBillable** имеет значение true.


### <a name="examples"></a>Примеры
Чтобы увидеть размер оплачиваемых событий для каждого компьютера, используйте свойство `_BilledSize`, которое предоставляет размер в байтах.

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
- Изучите статью [Начало работы с запросами журналов Azure Monitor](../../azure-monitor/log-query/get-started-queries.md).
- См. статью [Объединения в запросах журнала Azure Monitor](../../azure-monitor/log-query/joins.md).
