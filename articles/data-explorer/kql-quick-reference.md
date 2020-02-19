---
title: Краткий справочник по ККЛ
description: Список полезных функций ККЛ и их определений с примерами синтаксиса.
author: yossi-karp
ms.author: v-yokarp
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: b53890afd10a3aee131ab3897d01e6b6fdf261a6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429271"
---
# <a name="kql-quick-reference"></a>Краткий справочник по ККЛ

В этой статье приведен список функций и их описание, которые помогут вам приступить к работе с языком запросов Kusto.

| Оператор или функция                               | Description                           | Синтаксис                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Фильтр, Поиск и условие**                      |**_Поиск нужных данных путем фильтрации или поиска_** |                      |
| [where](/azure/kusto/query/whereoperator.md)                      | Фильтры для определенного предиката           | `T | where Predicate`                         |
| [где Contains/имеет](/azure/kusto/query/whereoperator.md)        | `Contains`: Поиск совпадения подстрок <br> `Has`: ищет конкретное слово (более высокую производительность)  | `T | where col1 contains/has "[search term]"`|
| [search](/azure/kusto/query/searchoperator.md)                    | Выполняет поиск значения во всех столбцах в таблице. | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](/azure/kusto/query/takeoperator.md)                        | Возвращает указанное число записей. Использование для проверки запроса<br>**_Примечание_** . `_take`_ и `_limit`_ являются синонимами. | `T | take NumberOfRows` |
| [ситуации](/azure/kusto/query/casefunction.md)                        | Добавляет оператор Condition, аналогичный if/then/ElseIf в других системах. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator.md)                | Создает таблицу с различным сочетанием указанных столбцов входной таблицы. | `distinct [ColumnName], [ColumnName]` |
| **Дата и время**                                   |**_Операции, использующие функции даты и времени_**               |                          |
|[прошлого](/azure/kusto/query/agofunction.md)                           | Возвращает смещение времени относительно времени выполнения запроса. Например, `ago(1h)` находится за один час до считывания текущего часового пояса. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction.md)  | Возвращает данные в [различных форматах даты](/azure/kusto/query/format-datetimefunction.md#supported-formats). | `format_datetime(datetime , format)` |
| [bin](/azure/kusto/query/binfunction.md)                          | Округляет все значения в интервале времени и группирует их | `bin(value,roundTo)` |
| **Создание и удаление столбцов**                   |**_Добавление или удаление столбцов в таблице_** |                                                    |
| [печатал](/azure/kusto/query/printoperator.md)                      | Выводит одну строку с одним или несколькими скалярными выражениями | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](/azure/kusto/query/projectoperator.md)                  | Выбирает столбцы для включения в указанный порядок. | `T | project ColumnName [= Expression] [, ...]` <br> либо <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator.md)         | Выбирает столбцы, исключаемые из выходных данных | `T | project-away ColumnNameOrPattern [, ...]` |
| [extend](/azure/kusto/query/extendoperator.md)                    | Создает вычисляемый столбец и добавляет его в результирующий набор | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Сортировка и агрегирование набора данных**                 |**_Реструктуризация данных путем их сортировки или группировки по понятным путям_**|                  |
| [sort](/azure/kusto/query/sortoperator.md)                        | Сортирует строки входной таблицы по одному или нескольким столбцам в порядке возрастания или убывания | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [В начало](/azure/kusto/query/topoperator.md)                          | Возвращает первые N строк набора данных при сортировке набора данных с помощью `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](/azure/kusto/query/summarizeoperator.md)              | Группирует строки в соответствии со столбцами группы `by` и вычисляет агрегаты для каждой группы. | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator.md)                       | Подсчитывает количество записей во входной таблице (например, T)<br>Этот оператор является сокращением для `summarize count() `| `T | count` |
| [join](/azure/kusto/query/joinoperator.md)                        | Объединяет строки двух таблиц для формирования новой таблицы, сопоставляя значения указанных столбцов из каждой таблицы. Поддерживает полный диапазон типов объединения: `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter`, `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](/azure/kusto/query/unionoperator.md)                      | Принимает две или более таблиц и возвращает все их строки | `[T1] | union [T2], [T3], …` |
| [range](/azure/kusto/query/rangeoperator.md)                      | Формирует таблицу с арифметическими последовательностями значений | `range columnName from start to stop step step` |
| **Форматирование данных**                                 | **_Переструктурирование данных для вывода в удобном виде_** | |
| [автоподстановки](/azure/kusto/query/lookupoperator.md)                    | Расширяет столбцы таблицы фактов значениями, которые были просмотрены в таблице измерения. | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [MV — развернуть](/azure/kusto/query/mvexpandoperator.md)               | Преобразует динамические массивы в строки (расширение для нескольких значений) | `T | mv-expand Column` |
| [проанализировать](/azure/kusto/query/parseoperator.md)                      | оценивает выражение строки и разбирает его на один или несколько вычисляемых столбцов. Используется для структурирования неструктурированных данных. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [Серия make](/azure/kusto/query/make-seriesoperator.md)          | Создает ряд указанных агрегированных значений вдоль указанной оси | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [сделаем](/azure/kusto/query/letstatement.md)                         | Привязывает имя к выражениям, которые могут ссылаться на связанное ему значение. Значения могут быть лямбда-выражениями для создания специальных функций в составе запроса. Используйте `let` для создания выражений по таблицам, результаты которых выглядят как новая таблица. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Общие сведения**                                     | **_Прочие операции и функции_** | |
| [invoke](/azure/kusto/query/invokeoperator.md)                    | Выполняет функцию для таблицы, которую она получает в качестве входных данных. | `T | invoke function([param1, param2])` |
| [Оценка pluginName](/azure/kusto/query/evaluateoperator.md)     | Оценивает расширения языка запросов (подключаемые модули) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Визуализация**                               | **_Операции, отображающие данные в графическом формате_** | |
| [обрабатывает](/azure/kusto/query/renderoperator.md) | Отображает результаты в виде графического вывода | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
