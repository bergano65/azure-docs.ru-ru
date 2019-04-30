---
title: Запросы оповещений журналов в Azure Monitor | Документация Майкрософт
description: Рекомендации по написанию эффективных запросов для оповещений журналов в обновлениях Azure Monitor и по преобразованию существующих запросов.
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: bwren
ms.subservice: alerts
ms.openlocfilehash: 429770b7651a93473c03f5e386d8f7b72692c161
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995983"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Запросы оповещений журналов в Azure Monitor
[Правила генерации оповещений на основе журналов Azure Monitor](alerts-unified-log.md) выполняются с регулярными интервалами, поэтому следует убедиться, что они написаны так, чтобы минимизировать накладные расходы и задержку. В этой статье приводятся рекомендации по написанию эффективных запросов для оповещений журналов и по преобразованию существующих запросов. 

## <a name="types-of-log-queries"></a>Типы запросов к журналу
[Журнал запросов в Azure Monitor](../log-query/log-query-overview.md) начинаться с таблицей или [поиска](/azure/kusto/query/searchoperator) или [объединение](/azure/kusto/query/unionoperator) оператор.

Например, следующий запрос к таблице _SecurityEvent_ ищет определенный идентификатор события. Это единственная таблица, которую должен обработать запрос.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Запросы, которые начинаются с `search` или `union`, позволяют выполнять поиск по нескольким столбцам в таблице или даже по нескольким таблицам. В следующих примерах показано несколько методов поиска термина _Memory_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Несмотря на то что `search` и `union` полезны при исследовании данных и поиске терминов по всей модели данных, они менее эффективны, чем использование имени таблицы, так как сканируют несколько таблиц. Запросы в правилах генерации оповещений выполняются с регулярными интервалами, что может привести к чрезмерным накладным расходам, которые добавляют задержку к оповещению. Из-за этих накладных расходов запросы правил генерации оповещений журнала в Azure всегда должны начинаться с имени таблицы, чтобы задать четкую область. Это повышает как производительность запросов, так и релевантность результатов.

## <a name="unsupported-queries"></a>Неподдерживаемые запросы
Начиная с 11 января 2019 года создание или изменение правил генерации оповещений журнала, которые используют операторы `search` или `union`, перестанет поддерживаться на портале Azure. Использование этих операторов в правилах генерации оповещений приведет к появлению сообщения об ошибке. Этому изменению не подвержены существующие правила генерации оповещений, а также правила, созданные и измененные с помощью API Log Analytics. Вам все равно следует рассмотреть возможность изменения любых правил генерации оповещений, которые используют эти типы запросов, хотя бы для повышения их эффективности.  

Правила генерации оповещений журнала, которые используют [запросы между ресурсами](../log-query/cross-workspace-query.md), не подвержены этому изменению, так как такие запросы используют оператор `union`, который ограничивает область запроса к определенным ресурсам. Это не эквивалентно оператору `union *`, который нельзя использовать.  Следующий пример будет действительным в правиле генерации оповещений журнала:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[Запрос между разными ресурсами](../log-query/cross-workspace-query.md) в оповещениях журнала поддерживается в новом API [правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). По умолчанию Azure Monitor использует [устаревшие API оповещения Log Analytics](api-alerts.md) для создания любого нового правила генерации оповещений на портале Azure, пока вы не переключаетесь с [устаревших API оповещений журнала](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). После переключения новый API используется по умолчанию для новых правил генерации оповещений на портале Azure и позволяет создавать правила генерации оповещений журнала запроса между разными ресурсами. Вы можете создать правила генерации оповещений журнала [запросов между разными ресурсами](../log-query/cross-workspace-query.md), не переключаясь, с помощью [шаблона ARM для API правил запросов по расписанию](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template), но этим правилом оповещения можно управлять с помощью [API правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), а не с помощью портала Azure.

## <a name="examples"></a>Примеры
Следующие примеры включают запросы журнала, которые используют операторы `search` и `union`, и предоставляют шаги по изменению этих запросов для использования их с правилами генерации оповещений.

### <a name="example-1"></a>Пример 1
Вы хотите создать правило генерации оповещений журнала, используя следующий запрос, который извлекает информацию о производительности с помощью `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Чтобы изменить этот запрос, нужно определить таблицу, к которой относятся свойства. Это делает следующий запрос:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

В результате запроса будет показано, что свойство _CounterName_ получено из таблицы _Perf_. 

С учетом этого имени таблицы вы можете создать следующий запрос, который можно использовать для правила генерации оповещений:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Пример 2
Вы хотите создать правило генерации оповещений журнала, используя следующий запрос, который извлекает информацию о производительности с помощью `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Чтобы изменить этот запрос, нужно определить таблицу, к которой относятся свойства. Это делает следующий запрос:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

В результате запроса будет показано, что свойства _ObjectName_ и _CounterName_ получены из таблицы _Perf_. 

С учетом этого имени таблицы вы можете создать следующий запрос, который можно использовать для правила генерации оповещений:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Пример 3

Вы хотите создать правило генерации оповещений журнала, используя следующий запрос, который извлекает информацию о производительности с помощью `search` и `union`: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Чтобы изменить этот запрос, нужно определить таблицу, к которой относятся свойства в первой части запроса: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

В результате запроса будет показано, что все свойства получены из таблицы _Perf_. 

Теперь используйте `union` с командой `withsource`, чтобы определить исходную таблицу, которая повлияла на каждую запись.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

В результате запроса будет показано, что эти свойства также получены из таблицы _Perf_. 

Эти результаты можно учесть для создания следующего запроса, который можно использовать для правила генерации оповещений: 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>Пример 4
Вы хотите создать правило генерации оповещений журнала, используя следующий запрос, который объединяет результаты двух запросов `search`:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```
 

Чтобы изменить этот запрос, нужно определить таблицу, которая содержит свойства в левой части объединения: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

В результате показано, что свойства в левой части объединения принадлежат таблице _SecurityEvent_. 

Теперь используйте следующий запрос, чтобы определить таблицу, которая содержит свойства в правой части объединения: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
В результате показано, что свойства в правой части объединения принадлежат таблице Heartbeat. 

Эти результаты можно учесть для создания следующего запроса, который можно использовать для правила генерации оповещений: 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения об [оповещениях журналов](alerts-log.md) в Azure Monitor.
- Дополнительные сведения о [запросах журналов](../log-query/log-query-overview.md).

