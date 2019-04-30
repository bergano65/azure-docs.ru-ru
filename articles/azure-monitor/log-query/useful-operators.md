---
title: Полезные операторы в запросах журнала Azure Monitor | Документация Майкрософт
description: Общие функции для различных сценариев в запросах журнала Azure Monitor.
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
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: d11445c3f31f9aced6fdb9783575d10a026de1f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424144"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Полезные операторы в запросах журнала Azure Monitor

В таблице ниже приведены некоторые общие функции, которые можно использовать для разных сценариев в запросах Azure Monitor.

## <a name="useful-operators"></a>Полезные операторы

Category                                |Соответствующая аналитическая функция
----------------------------------------|----------------------------------------
Псевдонимы выделений и столбцов            |`project`, `project-away`, `extend`
Временные таблицы и константы          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Операторы сравнения и строк         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Общие строковые функции                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Общие математические функции                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Анализ текста                            |`extract()`, `extractjson()`, `parse`, `split()`
Ограничение выходных данных                         |`take`, `limit`, `top`, `sample`
Функции данных                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Группирование и статистический анализ                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Соединения и объединения                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Порядок, сортировка                             |`sort`, `order` 
Динамический объект (JSON и массив)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Логические операторы                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Машинное обучение                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Дальнейшие действия

- Пройдите урок по [написанию запросов к журналу в Azure Monitor](get-started-queries.md).
