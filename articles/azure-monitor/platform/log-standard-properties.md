---
title: Стандартные свойства в записях журнала Azure Monitor | Документация Майкрософт
description: Описание свойств, общих для многих типов данных в журналах Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/20/2019
ms.author: bwren
ms.openlocfilehash: 50804e1f6ab4f352239d3f405e5b41e4e0c58d14
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67292818"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Стандартные свойства в журналов Azure Monitor
Данные в журналов Azure Monitor [сохраняется в виде набора записей в рабочей области Log Analytics или Application Insights приложения](../log-query/logs-structure.md), каждый с типом данных, который имеет уникальный набор свойств. Большинство типов данных имеют стандартные свойства, которые являются общими для нескольких типов. В этой статье описаны эти свойства и приведены примеры по их использованию в запросах.

Некоторые из этих свойств еще находятся в процессе реализации, поэтому они могут отображаться только для отдельных типов данных.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated и отметку времени
**TimeGenerated** (рабочей области Log Analytics) и **timestamp** свойства (приложение Application Insights) содержат дату и время создания записи. Это общее свойство позволяет фильтровать и суммировать данные по времени. Если выбран диапазон времени для представления или панели мониторинга на портале Azure, в нем TimeGenerated или метки времени для фильтрации результатов.

### <a name="examples"></a>Примеры

Указанный ниже запрос возвращает количество событий с ошибками за каждый день на прошлой неделе.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

Следующий запрос возвращает количество исключения, созданные за каждый день предыдущей недели.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="type-and-itemtype"></a>Тип и itemType
**Тип** (рабочей области Log Analytics) и **itemType** удержание свойства (приложение Application Insights) имя таблицы, что запись была восстановлена из которых может также рассматриваться как запись тип. Это свойство можно использовать в запросах, где объединяются записи из нескольких таблиц, например использующих оператор `search`, чтобы различать записи разных типов. В некоторых случаях вместо **Type** можно использовать **$table**.

### <a name="examples"></a>Примеры
Указанный ниже запрос возвращает количество записей по типу, собранных за последний час.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type
```

## <a name="resourceid"></a>\_ResourceId
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

В следующем запросе анализирует **_ResourceId** и статистические выражения в счет тома данных на одну подписку Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Используйте эти запросы `union withsource = tt *` только в случае необходимости, так как сканирование по типам данных требует больших затрат на выполнение.

## <a name="isbillable"></a>\_IsBillable
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

## <a name="billedsize"></a>\_BilledSize
Свойство **\_BilledSize** определяет размер данных в байтах, которые будут оплачиваться в вашей учетной записи Azure, если свойство **\_IsBillable** имеет значение true.

### <a name="examples"></a>Примеры
Чтобы увидеть размер оплачиваемых событий для каждого компьютера, используйте свойство `_BilledSize`, которое предоставляет размер в байтах.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Чтобы просмотреть размер оплаты события, принятые на подписку, используйте следующий запрос:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Чтобы просмотреть размер оплачиваемых событий, принятых на одну группу ресурсов, используйте следующий запрос:

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

Чтобы увидеть число платных типов данных с конкретного компьютера, используйте следующий запрос:

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
