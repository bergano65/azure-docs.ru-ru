---
title: Функции преобразования потока данных структурирование в фабрике данных Azure
description: Общие сведения о доступных функциях потока данных структурирование в фабрике данных Azure
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e63c3f329cb9c1fd5ca91274540f5145c3ad098a
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921545"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Функции преобразования в потоке данных структурирование

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Поток данных структурирование в фабрике данных Azure позволяет выполнять динамическую подготовку данных и структурирование в масштабе облака. Поток данных структурирование интегрируется с [Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) и делает функции Power Query M доступными для структурирование данных с помощью выполнения Spark. 

> [!NOTE]
> Поток данных структурирование в настоящее время доступных в общедоступной предварительной версии

В настоящее время не все функции Power Query M поддерживаются для структурирование данных, несмотря на то, что они доступны во время разработки. При построении потоков данных структурирование вам будет предложено следующее сообщение об ошибке, если функция не поддерживается:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Ниже приведен список поддерживаемых функций Power Query M.

## <a name="column-management"></a>Управление столбцами

* Выбор: [Table. SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Удаление: [Table. ремовеколумнс](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Переименование: [Table. RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [таблица. префиксколумнс](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table. трансформколумннамес](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Изменение порядка: [Table. реордерколумнс](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Фильтрация строк

Функция M [Table. селектровс](https://docs.microsoft.com/powerquery-m/table-selectrows) используется для фильтрации по следующим условиям.

* Равенство и неравенство
* Сравнения чисел, текста и дат (но не даты и времени)
* Числовые данные, например [Number. четный](https://docs.microsoft.com/powerquery-m/number-iseven), / [четные](https://docs.microsoft.com/powerquery-m/number-iseven)
* Текстовое вложение с использованием [Text. Contains](https://docs.microsoft.com/powerquery-m/text-contains), [Text. StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)или [Text. EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Диапазоны дат, включая все функции "Исин' [Date](https://docs.microsoft.com/powerquery-m/date-functions)". 
* Сочетания этих операторов с операторами AND, OR и not

## <a name="adding-and-transforming-columns"></a>Добавление и преобразование столбцов

Следующие функции M добавляют или преобразуют столбцы: [таблица. addColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [таблица. трансформколумнс](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [таблица. реплацевалуе](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table. дупликатеколумн](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Ниже приведены поддерживаемые функции преобразования.

* Числовые арифметические операции
* Сцепление текста
* Date Андтиме арифметика (арифметические операторы, [Date. AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date. аддмонсс](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date. аддкуартерс](https://docs.microsoft.com/powerquery-m/date-addquarters), [Date. аддвикс](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date. аддеарс](https://docs.microsoft.com/powerquery-m/date-addyears))
* Длительности можно использовать для арифметических операций с датами и временем, но они должны быть преобразованы в другой тип перед записью в приемник (арифметические операторы, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [продолжительность. Days](https://docs.microsoft.com/powerquery-m/duration-days), [Duration. Hours](https://docs.microsoft.com/powerquery-m/duration-hours), [Duration.](https://docs.microsoft.com/powerquery-m/duration-minutes) [reтоталхаурс](https://docs.microsoft.com/powerquery-m/duration-totalhours) [, Duration](https://docs.microsoft.com/powerquery-m/duration-totalminutes). [Seconds](https://docs.microsoft.com/powerquery-m/duration-seconds) [, Duration.](https://docs.microsoft.com/powerquery-m/duration-totaldays) [тоталсекондс](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* Большинство стандартных, научных и тригонометрических числовых функций (всех функций в рамках [операций](https://docs.microsoft.com/powerquery-m/number-functions#operations), [округления](https://docs.microsoft.com/powerquery-m/number-functions#rounding)и [тригонометрических](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) , *за исключением* Number. факториал, Number. перестановок и числа. сочетаний)
* Замена (замещаемый[. реплацетекст](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [Place. реплацевалуе](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text. Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text. Replace](https://docs.microsoft.com/powerquery-m/text-remove))
* Извлечение позиционированного текста ([Text. поситионоф](https://docs.microsoft.com/powerquery-m/text-positionof), [Text. length](https://docs.microsoft.com/powerquery-m/text-length), [Text. Start](https://docs.microsoft.com/powerquery-m/text-start), [Text. end](https://docs.microsoft.com/powerquery-m/text-end), [Text. Ближний](https://docs.microsoft.com/powerquery-m/text-middle), [Text. реплацеранже](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text. ремоверанже](https://docs.microsoft.com/powerquery-m/text-removerange))
* Основное форматирование текста ([Text. Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text. Upper](https://docs.microsoft.com/powerquery-m/text-upper), [Text. Trim](https://docs.microsoft.com/powerquery-m/text-trim) / [Начало](https://docs.microsoft.com/powerquery-m/text-trimstart) / [End](https://docs.microsoft.com/powerquery-m/text-trimend), [Text. падстарт](https://docs.microsoft.com/powerquery-m/text-padstart) / [End](https://docs.microsoft.com/powerquery-m/text-padend), [Text. Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Функции даты и времени ([Дата. день](https://docs.microsoft.com/powerquery-m/date-day), [Дата. месяц](https://docs.microsoft.com/powerquery-m/date-month), [Дата. год,](https://docs.microsoft.com/powerquery-m/date-year) [время. час](https://docs.microsoft.com/powerquery-m/time-hour), [время. минута](https://docs.microsoft.com/powerquery-m/time-minute), [время. секунд](https://docs.microsoft.com/powerquery-m/time-second), [Дата. DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [Date. DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [Date. дайсинмонс](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Выражения if (но ветви должны иметь совпадающие типы)
* Фильтры строк в качестве логического столбца
* Константы числа, текста, логических, дат и даты и времени

<a name="mergingjoining-tables"></a>Слияние и объединение таблиц
----------------------
* Power Query создаст вложенное соединение (Table. Нестеджоин; пользователи могут также вручную написать [таблицу. адджоинколумн](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    Пользователи должны развернуть вложенный столбец JOIN в невложенное соединение (Table. Експандтаблеколумн, не поддерживается в любом другом контексте).
* Таблица функций M [. Join](https://docs.microsoft.com/powerquery-m/table-join) может быть написана напрямую, чтобы избежать необходимости в дополнительном шаге расширения, но пользователь должен убедиться в отсутствии повторяющихся имен столбцов между соединяемыми таблицами.
* Поддерживаемые типы соединений: [inner](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Оба [значения. Equals](https://docs.microsoft.com/powerquery-m/value-equals) и [value. нуллабликуалс](https://docs.microsoft.com/powerquery-m/value-nullableequals) поддерживаются как компараторы равенства ключей

## <a name="group-by"></a>Group by

Используйте [Table. Group](https://docs.microsoft.com/powerquery-m/table-group) для статистической обработки значений.
* Должен использоваться с агрегатной функцией
* Поддерживаемые статистические функции: [Table. ROWCOUNT](https://docs.microsoft.com/powerquery-m/table-rowcount), [List. Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List. Count](https://docs.microsoft.com/powerquery-m/list-count), [List. СРЗНАЧ](https://docs.microsoft.com/powerquery-m/list-average), [List. min](https://docs.microsoft.com/powerquery-m/list-min), [List. Max](https://docs.microsoft.com/powerquery-m/list-max), [List. стандартное отклонение](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List. First](https://docs.microsoft.com/powerquery-m/list-first), [List. Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Сортировка

Используйте [таблицу. Sort](https://docs.microsoft.com/powerquery-m/table-sort) для сортировки значений.

## <a name="reducing-rows"></a>Сокращение количества строк

Сохраняются и удаляются первые, сохраняют диапазоны (соответствующие функции M, только вспомогательные счетчики, not Conditions: [Table. FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [таблица. Skip](https://docs.microsoft.com/powerquery-m/table-skip), [таблица. ремовефирстн](https://docs.microsoft.com/powerquery-m/table-removefirstn), [таблица. диапазон](https://docs.microsoft.com/powerquery-m/table-range), [таблица. Минн](https://docs.microsoft.com/powerquery-m/table-minn), [Table. максн](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Известные неподдерживаемые функции

| Функция | Состояние |
| -- | -- |
| Table.PromoteHeaders | Не поддерживается. Тот же результат можно получить, установив в наборе данных "первая строка как заголовок". |
| Table.CombineColumns | Это распространенный сценарий, который не поддерживается напрямую, но может быть достигнут путем добавления нового столбца, объединяющего два заданных столбца.  Например, Table. AddColumn (Ремовимаилколумн, "Name", each [FirstName] & "" & [LastName]) |
| Table.TransformColumnTypes | В большинстве случаев это поддерживается. Следующие сценарии не поддерживаются: преобразование строки в тип валюты, преобразование строки в тип времени, преобразование строки в процентный тип. |
| Table.NestedJoin | При простом соединении будет возникать ошибка проверки. Столбцы должны быть развернуты, чтобы они работали. |
| Table.Distinct | Удаление повторяющихся строк не поддерживается. |
| Table.RemoveLastN | Удаление нижних строк не поддерживается. |
| Table.RowCount | Не поддерживается, но может быть достигнут с добавлением столбца со всеми ячейками Empty (можно использовать столбец условий), а затем с помощью инструкции Group By для этого столбца. Таблица. Группа поддерживается. | 
| Обработка ошибок на уровне строк | Обработка ошибок на уровне строк в настоящее время не поддерживается. Например, чтобы отфильтровать нечисловые значения из столбца, одним из подходов будет преобразование текстового столбца в число. Каждая ячейка, для которой не выполняется преобразование, будет находиться в состоянии ошибки и должна быть отфильтрована. Этот сценарий невозможен в потоке данных структурирование. |
| Table.Transpose | Не поддерживается |
| Table.Pivot | Не поддерживается |

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [создать поток данных структурирование](wrangling-data-flow-tutorial.md).