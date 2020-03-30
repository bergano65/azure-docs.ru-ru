---
title: Быстрая ссылка на КЗЛ
description: Список полезных функций КЗЛ и их определения с примерами синтаксиса.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139085"
---
# <a name="kql-quick-reference"></a>Быстрая ссылка на КЗЛ

В этой статье показан список функций и их описания, которые помогут вам начать использовать язык квини Kusto.

| Оператор/Функция                               | Описание                           | Синтаксис                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Фильтр/Поиск/Состояние**                      |**_Найти соответствующие данные путем фильтрации или поиска_** |                      |
| [where](/azure/kusto/query/whereoperator)                      | Фильтры на определенном предикате           | `T | where Predicate`                         |
| [где содержит/имеет](/azure/kusto/query/whereoperator)        | `Contains`: Ищет любой подстроки матч <br> `Has`: Ищет конкретное слово (лучшая производительность)  | `T | where col1 contains/has "[search term]"`|
| [Поиск](/azure/kusto/query/searchoperator)                    | Поиск всех столбцов в таблице для значения | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [Принять](/azure/kusto/query/takeoperator)                        | Возвращает указанное количество записей. Используйте для тестирования запроса<br>**_Примечание:_** `_take`q `_limit`и q являются синонимами. | `T | take NumberOfRows` |
| [Случае](/azure/kusto/query/casefunction)                        | Добавляет условие, аналогичное if/then/elseif в других системах. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [Различных](/azure/kusto/query/distinctoperator)                | Производит таблицу с отчетливым сочетанием предоставленных столбцов таблицы ввода | `distinct [ColumnName], [ColumnName]` |
| **Дата/время**                                   |**_Операции, которые используют функции даты и времени_**               |                          |
|[Назад](/azure/kusto/query/agofunction)                           | Возвращает время, смещенное по отношению к времени выполнения запроса. Например, `ago(1h)` это за час до чтения текущих часов. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | Возвращает данные в [различных форматах дат.](/azure/kusto/query/format-datetimefunction#supported-formats) | `format_datetime(datetime , format)` |
| [Бен](/azure/kusto/query/binfunction)                          | Раунды всех значений в таймфрейме и группируют их | `bin(value,roundTo)` |
| **Создание/удаление столбцов**                   |**_Добавление или удаление столбцов в таблице_** |                                                    |
| [Печати](/azure/kusto/query/printoperator)                      | Выводы одной строки с одним или одним из скалярных выражений | `print [ColumnName =] ScalarExpression [',' ...]` |
| [Проекта](/azure/kusto/query/projectoperator)                  | Выберите столбцы для включения в указанный порядок | `T | project ColumnName [= Expression] [, ...]` <br> либо <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator)         | Выбирает столбцы, чтобы исключить из вывода | `T | project-away ColumnNameOrPattern [, ...]` |
| [Расширить](/azure/kusto/query/extendoperator)                    | Создает вычисленная колонка и добавляет его в набор результатов | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Сортировка и агрегированный набор данных**                 |**_Реструктуризация данных путем сортировки или группировки их значимыми способами_**|                  |
| [Сортировки](/azure/kusto/query/sortoperator)                        | Сортирует ряды таблицы ввода одним или более столбиками в порядке восходящего или убывающего | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [Вверх](/azure/kusto/query/topoperator)                          | Возвращает первые строки N набора данных при сортировке набора данных с помощью`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Суммировать](/azure/kusto/query/summarizeoperator)              | Группирует строки в `by` соответствии с столбиками группы и вычисляет агрегации по каждой группе | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | Подсчитывает записи в таблице ввода (например, T)<br>Этот оператор является сокращением для`summarize count() `| `T | count` |
| [Присоединиться к](/azure/kusto/query/joinoperator)                        | Слияние строк двух таблиц для формирования новой таблицы путем сопоставления значений указанного столбца (ы) из каждой таблицы. Поддерживает полный спектр типов `flouter` `inner`соединений: , `rightanti` `rightantisemi`, `rightouter` `innerunique` `leftanti`, `leftantisemi`, `leftouter` `leftsemi`, ,`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Союза](/azure/kusto/query/unionoperator)                      | Занимает две или более таблиц и возвращает все свои строки | `[T1] | union [T2], [T3], …` |
| [Диапазон](/azure/kusto/query/rangeoperator)                      | Создает таблицу с арифметической серией значений | `range columnName from start to stop step step` |
| **Форматирование данных**                                 | **_Реструктурировать данные на вывод полезным способом_** | |
| [Поиска](/azure/kusto/query/lookupoperator)                    | Расширяет столбцы таблицы фактов с значениями, просматриваемыми в таблице измерений | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](/azure/kusto/query/mvexpandoperator)               | Превращает динамические массивы в ряды (расширение многозначения) | `T | mv-expand Column` |
| [Анализа](/azure/kusto/query/parseoperator)                      | оценивает выражение строки и разбирает его на один или несколько вычисляемых столбцов. Используется для структурирования неструктурированных данных. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](/azure/kusto/query/make-seriesoperator)          | Создает ряд указанных агрегированных значений вдоль заданной оси | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [Позвольте](/azure/kusto/query/letstatement)                         | Связывает имя с выражениями, которые могут относиться к его связанной стоимости. Значения могут быть выражениями lambda для создания специальных функций как части запроса. Используйте `let` для создания выражений над таблицами, результаты которых выглядят как новая таблица. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Общие**                                     | **_Разное функционирование и функция_** | |
| [invoke](/azure/kusto/query/invokeoperator)                    | Запускает функцию на столе, которую она получает в качестве ввода. | `T | invoke function([param1, param2])` |
| [оценить pluginName](/azure/kusto/query/evaluateoperator)     | Оценивает расширение языка запросов (плагины) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Визуализации**                               | **_Операции, отображающие данные в графическом формате_** | |
| [Визуализации](/azure/kusto/query/renderoperator) | Рендеринг результатов как графический выход | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
