---
title: Объединения в запросах журнала Azure Monitor | Документация Майкрософт
description: Эта статья содержит урок по использованию объединений в запросах журнала Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 526c359367271c69ccd461e4421c3223b00fbc36
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900286"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Объединения в запросах журнала Azure Monitor

> [!NOTE]
> Перед выполнением этого занятия необходимо завершить [работу с Azure Monitor log Analytics](get-started-portal.md) и [Azure Monitor запросов к журналу](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Объединения позволяют анализировать данные из нескольких таблиц в одном запросе. Они объединяют строки двух наборов данных, сопоставляя значения из указанных столбцов.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

В этом примере первый набор данных фильтрует все действия входа. Он объединяется со вторым набором данных, который фильтрует действия выхода. Используемые столбцы: _Computer_, _Account_, _TargetLogonId_ и _TimeGenerated_. Наборы данных сопоставляются по общему столбцу _TargetLogonId_. Результат — одна запись на сопоставление, в которой есть и время входа и время выхода.

Если оба набора данных имеют столбцы с одинаковыми именами, столбцам набора данных справа присваивается номер индекса. Этот пример отобразит столбец _TargetLogonId_ со значениями в левой таблице и столбец  _TargetLogonId1_ со значениями в правой. В этом случае второй столбец _TargetLogonId1_ был удален с помощью оператора `project-away`.

> [!NOTE]
> Для повышения производительности оставьте только соответствующие столбцы объединенных наборов данных, используя оператор `project`.


Используйте следующий синтаксис, чтобы объединить два набора данных и чтобы присоединенный ключ имел разные имена между двумя таблицами:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Таблицы подстановки
Обычно для объединения применяется статическое сопоставление значений с использованием `datatable`, что может помочь в преобразовании результатов в удобный вид. Например, для добавления к данным событий безопасности имени события для каждого идентификатора.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![Объединение с datatable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Типы объединения
Укажите тип объединения с использованием аргумента _kind_. Каждый тип выполняет другое сопоставление между записями заданных таблиц, как описывается в следующей таблице.

| Тип соединения | Описание |
|:---|:---|
| innerunique | Это режим объединения по умолчанию. Сначала находятся значения соответствующего столбца в левой таблице, а повторяющиеся значения удаляются.  Затем набор уникальных значений сопоставляется с правой таблицей. |
| Внутреннее | В результаты включаются только совпадающие записи в обеих таблицах. |
| leftouter | В результаты включаются все записи из левой таблицы и соответствующие записи из правой таблицы. Выходные данные без соответствий содержат значения NULL.  |
| leftanti | В результаты включаются все записи из левой части, для которых нет соответствий в правой. Таблица результатов содержит только столбцы из левой таблицы. |
| leftsemi | В результаты включаются все записи из левой части, для которых есть соответствия в правой. Таблица результатов содержит только столбцы из левой таблицы. |


## <a name="best-practices"></a>Рекомендации

Для обеспечения оптимальной производительности учитывайте следующие рекомендации:

- Используйте фильтр времени в каждой таблице для сокращения записей, которые должны оцениваться для объединения.
- Перед оператором объединения используйте `where` и `project`, чтобы сократить количество строк и столбцов во входных таблицах.
- Если одна таблица меньше другой, используйте ее в качестве левой части объединенных данных.


## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с дополнительными уроками об использовании запросов журнала Azure Monitor.

- [Работа со строками](string-operations.md)
- [Статистические функции в запросах Log Analytics](aggregations.md)
- [Расширенные статистические функции в запросах Azure Log Analytics](advanced-aggregations.md)
- [Работа с JSON и структурами данных в запросах Log Analytics](json-data-structures.md)
- [Составление расширенных запросов](advanced-query-writing.md)
- [Создание графиков](charts.md)