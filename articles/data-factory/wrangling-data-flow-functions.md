---
title: Функции преобразования потока данных в Azure Data Factory
description: Обзор доступных функций потока потоков данных в Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 7235e95e5b33fb931411a51796a8dbec96c46355
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417663"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Функции преобразования в потоке данных споры

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Поток данных в Azure Data Factory позволяет осуществлять подготовку гибких данных без кода и пререканий в облаке. Поток данных с вражается с [Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) и делает функции Power Query M доступными для обработки данных с помощью выполнения искры. 

В настоящее время не все функции Power Query M поддерживаются для обработки данных, несмотря на то, что они доступны во время авторизации. При построении потоков данных споры, вы будете предложено со следующим сообщением об ошибке, если функция не поддерживается:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Ниже приведен список поддерживаемых функций энергосажий запрос M.

## <a name="column-management"></a>Управление колонками

* Выбор: [Таблица.ВыбратьКолонки](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Удаление: [Таблица.RemoveКолонки](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Переименование: [Table.RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table.PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Переупорядочение: [Таблица.ReorderКолонки](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Строка фильтрации

Используйте функцию M [Table.SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) для фильтрации на следующих условиях:

* Равенство и неравенство
* Число, текст и дата сравнения (но не DateTime)
* Числовая информация, такая как [Number.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[Odd](https://docs.microsoft.com/powerquery-m/number-iseven)
* Содержание текста с помощью [Text.Contains,](https://docs.microsoft.com/powerquery-m/text-contains) [Text.StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)или [Text.EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Дата колеблется, включая все функции 'IsIn' [Дата)](https://docs.microsoft.com/powerquery-m/date-functions) 
* Комбинации этих использования и, или, или нет условий

## <a name="adding-and-transforming-columns"></a>Добавление и преобразование столбцов

Следующие функции M добавить или преобразовать столбцы: [Table.AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Таблица.TransformКолонки](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Таблица.ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Таблица.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Ниже приведены поддерживаемые функции преобразования.

* Численная арифметика
* Текстовая конкатация
* Дата и время арифметика (Арифметические операторы, [Date.AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date.AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date.AddКвартал ,](https://docs.microsoft.com/powerquery-m/date-addquarters) [Date.AddНедели , Date.AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date.AddYears , Date.AddYears , Date.AddYears , Date.AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Продолжительности могут быть использованы для арифметики даты и времени, но должны быть преобразованы в другой тип, прежде чем писать в раковину (арифметические операторы, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [Duration.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds) [Продолжительность.Days](https://docs.microsoft.com/powerquery-m/duration-days), [Duration.Hours](https://docs.microsoft.com/powerquery-m/duration-hours), [Продолжительность.Минута.Минуты](https://docs.microsoft.com/powerquery-m/duration-minutes), [Продолжительность.Секунды](https://docs.microsoft.com/powerquery-m/duration-seconds), [Duration.TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays) [Продолжительность.TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalminutes) [Duration.TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours)    
* Большинство стандартных, научных и тригонометрических численных функций (все функции в рамках [операций,](https://docs.microsoft.com/powerquery-m/number-functions#operations) [округления](https://docs.microsoft.com/powerquery-m/number-functions#rounding)и [тригонометрии,](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *за исключением* Number.Factorial, Number.Перестановки, и Number.Combinations)
* Замена[(Replacer.ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text.Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text.Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Позиционный текст экстракции ([Text.Positionof](https://docs.microsoft.com/powerquery-m/text-positionof), [Text.Length](https://docs.microsoft.com/powerquery-m/text-length), [Text.Start](https://docs.microsoft.com/powerquery-m/text-start), [Text.End](https://docs.microsoft.com/powerquery-m/text-end), [Text.Middle](https://docs.microsoft.com/powerquery-m/text-middle), [Text.ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Базовый формат текста ([Text.Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text.Upper](https://docs.microsoft.com/powerquery-m/text-upper), [Text.Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End](https://docs.microsoft.com/powerquery-m/text-trimend), [Text.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend), [Text.Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Дата / Время Функции[(Date.Day](https://docs.microsoft.com/powerquery-m/date-day), [Date.Month](https://docs.microsoft.com/powerquery-m/date-month), [Date.year](https://docs.microsoft.com/powerquery-m/date-year) [time.Hour](https://docs.microsoft.com/powerquery-m/time-hour), [Time.Minute](https://docs.microsoft.com/powerquery-m/time-minute), [Time.Second](https://docs.microsoft.com/powerquery-m/time-second), [Date.DayOfweek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [Date.DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [Date.DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Если выражения (но ветви должны иметь соответствующие типы)
* Фильтры строки как логический столбец
* Число, текст, логические, даты и константы времени даты

<a name="mergingjoining-tables"></a>Слияние/присоединение таблиц
----------------------
* Power Query будет генерировать вложенное соединение (Table.NestedJoin; пользователи также могут вручную написать [Table.AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    Пользователи должны затем расширить вложенный столбец соединения в невложенное соединение (Table.ExpandTableColumn, не поддерживаемый в любом другом контексте).
* Функция M [Table.Join](https://docs.microsoft.com/powerquery-m/table-join) может быть написана непосредственно, чтобы избежать необходимости дополнительного шага расширения, но пользователь должен убедиться, что среди объединенных таблиц нет дублирующих названий столбцов
* Поддерживаемые виды соединения: [Внутренний](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Как [Value.Equals, так](https://docs.microsoft.com/powerquery-m/value-equals) и [Value.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) поддерживаются в качестве ключевых сравниваемых равных

## <a name="group-by"></a>Group by

Используйте [Table.Group](https://docs.microsoft.com/powerquery-m/table-group) для агрегирования значений.
* Должны использоваться с функцией агрегирования
* Поддерживаемые функции агрегации: [Table.RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [List.Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List.Count](https://docs.microsoft.com/powerquery-m/list-count), [List.Average](https://docs.microsoft.com/powerquery-m/list-average), [List.Min](https://docs.microsoft.com/powerquery-m/list-min), [List.Max](https://docs.microsoft.com/powerquery-m/list-max), [List.StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List.First](https://docs.microsoft.com/powerquery-m/list-first), [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Сортировка

Используйте [Table.Sort](https://docs.microsoft.com/powerquery-m/table-sort) для сортировки значений.

## <a name="reducing-rows"></a>Уменьшение строк

Держите и удалите Top, Keep Range (соответствующие функции M, только поддержка рассчитывает, а не условия: [Table.FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table.Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table.RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table.Range](https://docs.microsoft.com/powerquery-m/table-range), [Table.MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Известные неподдерживаемые функции

| Компонент | Состояние |
| -- | -- |
| Table.PromoteHeaders | Не поддерживается. Такой же результат может быть достигнут путем установки "Первый ряд как заголовок" в наборе данных. |
| Table.CombineColumns | Это распространенный сценарий, который напрямую не поддерживается, но может быть достигнут путем добавления нового столбца, который совместляет два данного столбца.  Например, table.AddColumn (RemoveEmailColumn, "Имя", каждый «Имя» & «& »Имя)) |
| Table.TransformColumnTypes | Это поддерживается в большинстве случаев. Следующие сценарии не поддерживаются: преобразование строки в тип валюты, преобразование строки в тип времени, преобразование строки в тип Процента. |
| Table.NestedJoin | Простое соединение приведет к ошибке проверки. Для работы столбцов необходимо расширить его работу. |
| Table.Distinct | Удаление дубликатов строк не поддерживается. |
| Table.RemoveLastN | Удаление нижних строк не поддерживается. |
| Table.RowCount | Не поддерживается, но может быть достигнуто с помощью столбца добавления со всеми ячейками пустыми (условие столбец может быть использован), а затем с помощью группы на этом столбце. Table.Group поддерживается. | 
| Обработка ошибок уровня строки | Обработка ошибок уровня строки в настоящее время не поддерживается. Например, для фильтрации нечислоных значений из столбца одним из подходов будет преобразование столбца текста в число. Каждая ячейка, которая не может трансформироваться, будет находиться в состоянии ошибки и должна быть отфильтрована. Этот сценарий невозможен в споре с потоком данных. |
| Table.Transpose | Не поддерживается |
| Table.Pivot | Не поддерживается |

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [создать поток данных споры.](wrangling-data-flow-tutorial.md)