---
title: Работа со значениями даты и времени в запросах журнала Azure Monitor | Документация Майкрософт
description: В этой статье описывается работа с данными даты и времени в запросах журнала Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d659be5b817317e7cec5726718f154825674349e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365348"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Работа со значениями даты и времени в запросах журнала Azure Monitor

> [!NOTE]
> Прежде чем приступить к этому уроку, необходимо ознакомиться со статьями [Начало работы с порталом аналитики](get-started-portal.md) и [Начало работы с запросами](get-started-queries.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

В этой статье описывается работа с данными даты и времени в запросах журнала Azure Monitor.


## <a name="date-time-basics"></a>Основные сведения о дате и времени
Язык запросов Kusto содержит два основных типа данных, связанных с датами и временем: datetime и timespan. Все даты задаются в формате UTC. Хотя поддерживаются несколько форматов даты и времени, рекомендуется использовать формат ISO8601. 

Интервалы времени выражаются как десятичное число, за которым следует единица времени:

|сокращение   | единица времени    |
|:---|:---|
|d           | day          |
|ч           | ч.         |
|мин           | minute       |
|с           | second       |
|ms          | миллисекунда  |
|микросекунда | микросекунда  |
|галочка        | наносекунда   |

Значения даты и времени можно создать путем преобразования строки с помощью оператора `todatetime`. Например, чтобы просмотреть пульс виртуальной машины, отправленный в определенный период времени, можно использовать оператор `between`, с помощью которого удобнее указывать диапазон времени.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Другим распространенным сценарием является сравнение даты и времени с настоящим временем. Например, чтобы просмотреть все пакеты пульса за последние две минуты, можно использовать оператор `now` вместе с промежутком времени в две минуты:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Для этой функции также доступно сокращение:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Кратчайший и самый удобочитаемый метод — использовать оператор `ago`:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Предположим, что вместо времени начала и окончания вы знаете время начала и длительность. Запрос можно переписать следующим образом:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Преобразование единиц измерения времени
Понадобится выразить дату и время или интервал времени в единицах времени, отличных от значения по умолчанию. Предположим, вы просматриваете события ошибок за последние 30 минут и нуждаетесь в вычисляемом столбце, который показывает, как давно произошло событие.

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Столбец `timeAgo` содержит такие значения: "00:09:31.5118992", то есть они форматируются как чч: мм: СС. fffffff. Если вы хотите форматировать эти значения в `numver` минут с времени начала, укажите timeAgo/1m.

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Агрегирование и группирование по интервалам времени
Другим очень распространенным сценарием является необходимость получения статистики за определенный период времени и для определенного интервала. Для этого можно использовать оператор `bin` как часть предложения итоговых значений.

Чтобы получить количество событий, которые происходили каждые 5 минут в течение последнего получаса, используйте следующий запрос:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

В результате вы получите следующую таблицу:  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Другой способ создания контейнеров результатов — использовать функции, такие как `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Вы получите следующие результаты:

|TIMESTAMP|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Часовые пояса
Поскольку все значения даты и времени выражены в формате UTC, возможно будет удобнее, если преобразовать эти значения в формат локального времени. Например, используйте это вычисление для преобразования времени UTC в формат PST (тихоокеанское время):

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Связанные функции

| Категория | Компонент |
|:---|:---|
| Преобразование типов данных | [ToDateTime](/azure/kusto/query/todatetimefunction)  [тотимеспан](/azure/kusto/query/totimespanfunction)  |
| Округление значения до размера ячейки | [bin](/azure/kusto/query/binfunction) |
| Получение конкретной даты или времени | [назад](/azure/kusto/query/agofunction) [](/azure/kusto/query/nowfunction)   |
| Получение части значения | [datetime_part](/azure/kusto/query/datetime-partfunction) [](/azure/kusto/query/getmonthfunction) [монсофеар](/azure/kusto/query/monthofyearfunction) [г](/azure/kusto/query/getyearfunction) . в [DayOfMonth](/azure/kusto/query/dayofmonthfunction) [DayOfWeek](/azure/kusto/query/dayofweekfunction) [DayOfYear](/azure/kusto/query/dayofyearfunction) [WeekOfYear](/azure/kusto/query/weekofyearfunction) |
| Получение значения относительной даты  | [ендофдай](/azure/kusto/query/endofdayfunction) [ендофвик](/azure/kusto/query/endofweekfunction) [EndOfMonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [стартофдай](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [StartOfMonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с другими статьями по использованию [языка запросов Kusto](/azure/kusto/query/) с данными журналов Azure Monitor.

- [Работа со строками](string-operations.md)
- [Статистические функции в запросах Log Analytics](aggregations.md)
- [Расширенные статистические функции в запросах Azure Log Analytics](advanced-aggregations.md)
- [Работа с JSON и структурами данных в запросах Log Analytics](json-data-structures.md)
- [Составление расширенных запросов](advanced-query-writing.md)
- [Joins](joins.md)
- [Создание графиков](charts.md)
