---
title: Справочник по фильтрам OData — Поиск Azure
description: Справочник по языку OData для синтаксиса фильтров в запросах поиска Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 8817ce075409a3f166b82404767697dc1326cc89
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647589"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Синтаксис $filter OData в поиске Azure

Поиск Azure использует [выражения фильтра OData](query-odata-filter-orderby-syntax.md) для применения дополнительных критериев к поисковому запросу, кроме терминов полнотекстового поиска. В этой статье подробно описывается синтаксис фильтров. Дополнительные общие сведения о том, что такое фильтры и как их использовать для реализации конкретных сценариев запросов, см. [в разделе Фильтры в службе поиска Azure](search-filters.md).

## <a name="syntax"></a>Синтаксис

Фильтр в языке OData является логическим выражением, которое, в свою очередь, может быть одним из нескольких типов выражений, как показано в следующем EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Доступна также интерактивная схема синтаксиса:

> [!div class="nextstepaction"]
> [Схема синтаксиса OData для поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md) .

Ниже перечислены типы логических выражений.

- Выражения фильтра коллекции с `any` помощью `all`или. Они применяют условия фильтра к полям коллекции. Дополнительные сведения см. [в разделе операторы коллекции OData в службе поиска Azure](search-query-odata-collection-operators.md).
- Логические выражения, которые объединяют другие логические выражения с помощью `and`операторов `or`, и `not`. Дополнительные сведения см. [в статье логические операторы OData в службе поиска Azure](search-query-odata-logical-operators.md).
- Выражения сравнения, которые сравнивают поля или переменные диапазона с постоянными значениями с помощью `eq`операторов `ne` `gt`, `lt` `ge`,,, и `le`. Дополнительные сведения см. [в разделе операторы сравнения OData в службе поиска Azure](search-query-odata-comparison-operators.md). Выражения сравнения также используются для сравнения расстояний между географическими пространственными координатами `geo.distance` с помощью функции. Дополнительные сведения см. [в статье геопространственные функции OData в службе поиска Azure](search-query-odata-geo-spatial-functions.md).
- Логические литералы `true` и `false`. Эти константы могут оказаться полезными иногда при программном создании фильтров, но в противном случае они не используются на практике.
- Вызовы логических функций, включая:
  - `geo.intersects`, который проверяет, находится ли заданная точка в пределах заданного многоугольника. Дополнительные сведения см. [в статье геопространственные функции OData в службе поиска Azure](search-query-odata-geo-spatial-functions.md).
  - `search.in`, который сравнивает переменную поля или диапазона с каждым значением в списке значений. Дополнительные сведения см. [в разделе `search.in` функция OData в службе поиска Azure](search-query-odata-search-in-function.md).
  - `search.ismatch`и `search.ismatchscoring`, которые выполняют операции полнотекстового поиска в контексте фильтра. Дополнительные сведения см. [в статье функции полнотекстового поиска OData в службе поиска Azure](search-query-odata-full-text-search-functions.md).
- Пути к полям или переменные диапазона типа `Edm.Boolean`. Например, если в индексе имеется логическое поле с именем `IsEnabled` и нужно вернуть все документы, в которых это поле имеет `true`значение, критерием фильтра может быть только имя `IsEnabled`.
- Логические выражения в круглых скобках. Использование круглых скобок может помочь в явном определении порядка операций в фильтре. Дополнительные сведения о приоритете операторов OData по умолчанию см. в следующем разделе.

### <a name="operator-precedence-in-filters"></a>Приоритет операторов в фильтрах

При написании критерия фильтра без круглых скобок вокруг его вложенных выражений Поиск Azure будет оценивать его в соответствии с набором правил приоритета операторов. Эти правила основаны на том, какие операторы используются для объединения подвыражений. В следующей таблице перечислены группы операторов в порядке убывания приоритета.

| Группа | Оператор (ы) |
| --- | --- |
| Логические операторы | `not` |
| Операторы сравнения | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Логические операторы | `and` |
| Логические операторы | `or` |

Оператор, наявляющийся выше в приведенной выше таблице, будет "более тесно" привязан к операндам по сравнению с другими операторами. Например, `and` имеет более высокий приоритет, чем `or`, а операторы сравнения имеют более высокий приоритет, чем любой из них, поэтому следующие два выражения эквивалентны:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not` Оператор имеет наивысший приоритет всех--даже выше, чем операторы сравнения. Вот почему, если вы попытаетесь написать фильтр следующим образом:

    not Rating gt 5

Вы получите следующее сообщение об ошибке:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Эта ошибка возникает потому, что оператор связан только `Rating` с полем, имеющим тип `Edm.Int32`, а не со всем выражением сравнения. Исправление заключается в том, чтобы поставить операнд `not` в круглые скобки:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Ограничения на размер выражений фильтрации

Существуют ограничения на размер и сложность выражений фильтрации, которые можно отправлять в Поиск Azure. Ограничения ориентировочно основаны на количестве предложений в выражении фильтрации. Хорошая рекомендация заключается в том, что если у вас есть сотни предложений, вы рискуете превысить лимит. Мы рекомендуем разрабатывать приложение таким образом, чтобы оно не создавало фильтры неограниченного размера.

> [!TIP]
> Использование функции вместо длинных точек сравнения на равенство может помочь избежать ограничения для предложения фильтра, так как вызов функции считается единственным предложением. [ `search.in` ](search-query-odata-search-in-function.md)

## <a name="examples"></a>Примеры

Найти все гостиницы, по крайней мере, с одной комнатой с базовой ставкой менее $200, которые оцениваются по 4 или выше.

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Найти все гостиницы, отличные от "Sea View Motel", которые были отремонтированных с 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Найти все гостиницы, которые были отремонтированных в 2010 или более поздней версии. Литерал DateTime включает сведения о часовом поясе для тихоокеанского стандартного времени:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Найти все гостиницы, в которых включена стоянка, и все комнаты не Курение:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- или -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Найти все отели класса люкс или отели с рейтингом 5, в которых есть парковка:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Найдите все гостиницы с тегом "WiFi" хотя бы в одном помещении (где каждая комната содержит теги, `Collection(Edm.String)` хранящиеся в поле):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Найти все гостиницы с любыми комнатами:  

    $filter=Rooms/any()

Найти все гостиницы, у которых нет комнат:

    $filter=not Rooms/any()

Найти все гостиницы в 10 километрах заданной контрольной точки ( `Location` где является полем типа `Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Найти все гостиницы в заданном окне просмотра, описанные в виде `Location` многоугольника (где является полем типа EDM. GeographyPoint). Многоугольник должен быть замкнутым, то есть первый и последний наборы точек должны быть одинаковыми. Кроме того, [точки должны быть указаны в порядке против часовой стрелки](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Найти все гостиницы, в которых поле "Описание" имеет значение null. Поле будет иметь значение null, если оно не было задано, или если оно было явно задано как NULL:  

    $filter=Description eq null

Найти все гостиницы с именем, равным "Sea View Motel" или "Budget Гостиницы". Эти фразы содержат пробелы, а пробел — разделитель по умолчанию. В качестве третьего строкового параметра можно указать альтернативный разделитель в одинарных кавычках:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Найти все гостиницы с именем, равным "Sea View Motel" или "Budget Гостиницы", разделенными "|"):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Найти все гостиницы, в которых все комнаты имеют тег "Wi" или "купание":

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Найти совпадение по фразам в коллекции, например "нагревани Товел стоек" или "хаирдрер включен" в тегах.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Найти документы со словом waterfront. Этот запрос фильтрации идентичен [поисковому запросу](https://docs.microsoft.com/rest/api/searchservice/search-documents) с `search=waterfront`:

    $filter=search.ismatchscoring('waterfront')

Найти документы со словом hostel и рейтингом, большим или равным 4, или документы со словом motel и рейтингом 5. Этот запрос не может быть выражен без `search.ismatchscoring` функции, так как он сочетает полнотекстовый поиск с операциями фильтрации `or`с помощью.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Найти документы без слова luxury.

    $filter=not search.ismatch('luxury')

Найти документы с фразой "ocean view" или рейтингом 5. Запрос `search.ismatchscoring` будет выполняться только по отношению к полям `HotelName` и `Description`. Документы, которые совпали только с вторым предложением дизъюнкции, будут возвращены — Гостиницы со `Rating` значением, равным 5. Эти документы будут возвращены с оценками, равными нулю, чтобы ясно, что они не соответствуют ни одной из оцененных частей выражения.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Поиск гостиниц, в которых слова "Гостиница" и "Аэропорт" имеют не более пяти слов, разделенных на описание, и там, где все комнаты не курение. В этом запросе используется [полный язык запросов Lucene](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Следующие шаги  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Общие сведения о языке выражений OData для поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
