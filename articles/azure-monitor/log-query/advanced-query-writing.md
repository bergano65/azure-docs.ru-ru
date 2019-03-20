---
title: Дополнительные запросы в Azure Monitor | Документация Майкрософт
description: В этой статье содержатся сведения об использовании портала аналитики для записи запросов в Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 65713ed9c2d0635e776a7a7e5f205b6d55438ed4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451626"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Составление расширенных запросов в Azure Monitor

> [!NOTE]
> Следует выполнить [приступить к работе с Azure Monitor Log Analytics](get-started-portal.md) и [начало работы с запросами](get-started-queries.md) перед завершением этого занятия.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Повторное использование кода с помощью оператора let
С помощью оператора `let` можно присвоить результаты переменной и ссылаться на нее позже в запросе:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Переменным также можно назначить постоянные значения. При этом можно настроить параметры для полей, которые необходимо менять каждый раз, когда вы выполняете запрос. Измените параметры нужным образом. Например, чтобы вычислить свободное дисковое пространство и объем доступной памяти (в процентилях) в заданный период времени:

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Таким образом можно легко изменить время начала и окончания во время очередного выполнения запроса.

### <a name="local-functions-and-parameters"></a>Локальные функции и параметры
С помощью оператора `let` можно создать функции, которые могут использоваться в одном запросе. Например, определите функцию, которая принимает поле datetime (в формате UTC) и преобразует его в стандартный формат США. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>Оператор print
`print` возвращает таблицу с одним столбцом и одной строкой, содержащей результаты вычисления. Это часто используется в случаях, когда необходимо простых вычислений. Например, чтобы найти текущее время в формате PST и добавить столбец с временем в формате EST.

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Оператор datatable
`datatable` позволяет определить набор данных. Укажите схему и набор значений, а затем передайте таблицу в любые другие элементы запроса. Например, чтобы создать таблицу использования ОЗУ и вычислить среднее значение в час:

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Конструкции datatable также очень полезны при создании таблицы подстановки. Например, чтобы сопоставить данные таблицы, такие как идентификаторы событий из таблицы _SecurityEvent_, с типами событий, перечисленными в другом месте, создайте таблицу подстановки с типами событий, используя оператор `datatable`, и соедините эту таблицу с данными _SecurityEvent_:

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с другими статьями по использованию [языка запросов Kusto](/azure/kusto/query/) с данными журналов Azure Monitor.

- [Работа со строками](string-operations.md)
- [Работа со значениями даты и времени](datetime-operations.md)
- [Статистические функции в запросах Log Analytics](aggregations.md)
- [Расширенные статистические функции в запросах Azure Log Analytics](advanced-aggregations.md)
- [Работа с JSON и структурами данных в запросах Log Analytics](json-data-structures.md)
- [Соединения](joins.md)
- [Создание графиков](charts.md)
