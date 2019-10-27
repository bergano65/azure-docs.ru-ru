---
title: Краткий справочник по запросу журнала SQL для Azure Monitor | Документация Майкрософт
description: Дополнительные сведения для пользователей, которые знакомы с SQL, при написании запросов журнала в Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 4acf3c2f8cee3ca9e679915eec677b6dd92792bf
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932913"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>Краткий справочник по запросам журнала SQL для Azure Monitor 

С помощью следующей таблицы пользователи, знакомые с SQL, смогут изучить язык запросов Kusto для составления запросов журнала в Azure Monitor. Ознакомьтесь с командой T-SQL для решения распространенных сценариев и эквивалента в Azure Monitor запросе журнала.

## <a name="sql-to-azure-monitor"></a>SQL в Azure Monitor

Описание                             |Запросы SQL                                                                                          |Запросы журнала Azure Monitor
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Выбор всех данных из таблицы            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Выбор определенных столбцов из таблицы    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
Выбор 100 записей из таблицы         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Оценка Null                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Сравнение строк: равенство             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Сравнение строк: подстрока            |`SELECT * FROM dependencies WHERE name like "%bcd%"`                                                   |<code>dependencies <br>&#124; where name contains "bcd"</code>
Сравнение строк: подстановочный знак             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Сравнение даты: за последний день             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Сравнение даты: диапазон дат             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Сравнение логических значений                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Сортировка                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
Уникальные значения                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Группирование, агрегирование                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Псевдонимы столбцов, расширение                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Первые n записей по мере                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Объединение множеств                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Объединение множеств: при выполнении условий                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5`                |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Объединение                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Дальнейшие действия

- Пройдите занятия по [написанию запросов журналов в Azure Monitor](get-started-queries.md).
