---
title: Справочник по фильтра OData — поиска Azure
description: Справочник по языку OData для синтаксис фильтра в запросах поиска Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: c44645ebcbf8808cc929bfaa0c3cb0d3ee9c90cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079916"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Синтаксис OData $filter в службе поиска Azure

Поиск Azure использует [выражения фильтров OData](query-odata-filter-orderby-syntax.md) для применения дополнительных условий в запросе, помимо полнотекстового поиска терминов. В этой статье описывается синтаксис фильтров подробно. Более общие сведения о фильтры и как использовать их для реализации сценариев конкретного запроса, см. в разделе [фильтры в службе поиска Azure](search-filters.md).

## <a name="syntax"></a>Синтаксис

Фильтр на языке OData — это логическое выражение, в свою очередь может принимать одно из нескольких типов выражений, как показано ниже EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

Также доступна схему интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> См. в разделе [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md) для завершения EBNF.

Следующие типы логических выражений:

- Коллекция выражений фильтра, с помощью `any` или `all`. Условия фильтра это применимо к коллекции полей. Дополнительные сведения см. в разделе [операторы коллекции OData в службе поиска Azure](search-query-odata-collection-operators.md).
- Логические выражения, объединяющие других логических выражений, с помощью операторов `and`, `or`, и `not`. Дополнительные сведения см. в разделе [OData логические операторы в службе поиска Azure](search-query-odata-logical-operators.md).
- Выражения сравнения, которые сравнение полей или переменные для значений констант, с помощью операторов диапазона `eq`, `ne`, `gt`, `lt`, `ge`, и `le`. Дополнительные сведения см. в разделе [операторов сравнения OData в службе поиска Azure](search-query-odata-comparison-operators.md). Выражения сравнения также используются для сравнения расстояний между геопространственные координаты, учитывая `geo.distance` функции. Дополнительные сведения см. в разделе [геопространственные функции OData в службе поиска Azure](search-query-odata-geo-spatial-functions.md).
- Логические литералы `true` и `false`. Эти константы можно использовать Иногда при программное создание фильтров, но в противном случае не обычно используются на практике.
- Вызовы логические функции, включая:
  - `geo.intersects`, который проверяет, является ли заданная точка находится в пределах указанного многоугольника. Дополнительные сведения см. в разделе [геопространственные функции OData в службе поиска Azure](search-query-odata-geo-spatial-functions.md).
  - `search.in`, который сравнивает переменную поля или диапазона, по каждому значению типа из списка значений. Дополнительные сведения см. в разделе [OData `search.in` функция в службе поиска Azure](search-query-odata-search-in-function.md).
  - `search.ismatch` и `search.ismatchscoring`, который выполнения операций полнотекстового поиска в контексте фильтра. Дополнительные сведения см. в разделе [OData функции полнотекстового поиска в службе поиска Azure](search-query-odata-full-text-search-functions.md).
- Поле пути или переменным типа диапазона `Edm.Boolean`. Например, если индекс содержит логическое поле с именем `IsEnabled` и требуется вернуть все документы, где это поле является `true`, выражение фильтра может просто быть именем `IsEnabled`.
- Логические выражения в круглых скобках. С помощью скобок может помочь явно определить порядок операций в фильтре. Дополнительные сведения приоритета по умолчанию операторов OData см. следующий раздел.

### <a name="operator-precedence-in-filters"></a>Приоритет операторов в фильтрах

При написании критерий фильтра без скобок вокруг его вложенных выражений, служба поиска Azure определяет его в соответствии с набором правил приоритета операторов. Эти правила основаны на какие операторы используются для объединения вложенных выражений. В следующей таблице перечислены группы операторов в порядке убывания в порядке убывания приоритета:

| Группа | Operator(s) |
| --- | --- |
| Логические операторы | `not` |
| Операторы сравнения | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Логические операторы | `and` |
| Логические операторы | `or` |

Оператор, который находится выше в таблице выше «привяжет более тесно» для своих операндов, чем другие операторы. Например `and` имеет более высокий приоритет, чем `or`, и операторы сравнения имеют более высокий приоритет, чем любая из них, поэтому следующие два выражения эквивалентны:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not` Оператор имеет наивысший приоритет всех — даже больше, чем операторы сравнения. Вот почему при попытке записи фильтра следующим образом:

    not Rating gt 5

Вы получите следующее сообщение об ошибке:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Эта ошибка возникает, так как оператор связан с просто `Rating` поле, которое имеет тип `Edm.Int32`и не с помощью выражения всей сравнения. Исправление — поместить операнд `not` в круглые скобки:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Ограничения на размер выражений фильтрации

Существуют ограничения на размер и сложность выражений фильтрации, которые можно отправлять в Поиск Azure. Ограничения ориентировочно основаны на количестве предложений в выражении фильтрации. Рекомендуется является то, что если у вас есть сотни предложений, вы риск превышения предела. Мы рекомендуем разработке приложения таким образом, что он не создавал фильтры неограниченного размера.

> [!TIP]
> С помощью [ `search.in` функция](search-query-odata-search-in-function.md) вместо long разделения равенства сравнения может помочь избежать ограничение предложение фильтра, так как вызов функции, считается одно предложение.

## <a name="examples"></a>Примеры

Найти все гостиницы с по крайней мере одной комнате с базовой меньше 200 долл. США, рейтингом равна или превышает 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Найти все гостиницы, отличные от «Motel Sea представление», отремонтированные после 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Найти все гостиницы, которые были отремонтированы в 2010 или более поздней версии. Литерал даты-времени включает в себя сведения о часовом поясе стандартное тихоокеанское время:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Найти все гостиницы с парковкой включены, где все комнаты, некурящих:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- или -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Найти все отели класса люкс или отели с рейтингом 5, в которых есть парковка:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Найти все гостиницы с тегом «wifi» в комнате, по крайней мере один (где помещений имеет теги, хранящиеся в `Collection(Edm.String)` поле):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Найти все гостиницы с любой комнаты:  

    $filter=Rooms/any()

Найти все гостиницы, у которых нет комнат:

    $filter=not Rooms/any()

Найти все гостиницы в пределах 10 километров от заданной контрольной точки (где `Location` является полем типа `Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Найти все гостиницы в пределах указанной области просмотра, описанной как многоугольник (где `Location` является полем типа Edm.GeographyPoint). Многоугольника должны быть закрыты, то есть первый и последний наборов точек должны быть одинаковыми. Кроме того [точки должны быть перечислены в порядке против часовой стрелки](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Найти все гостиницы, где поле «Description» имеет значение null. Поле будет иметь значение null, если он не был задан или если оно было явно задано значение null:  

    $filter=Description eq null

Найти все гостиницы с именем, равным «Представление Sea motel» или «Бюджета hotel»). Эти фразы содержать пробелы и места — разделитель по умолчанию. Можно указать альтернативные разделитель в одинарные кавычки в качестве третьего параметра строки:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Найти все гостиницы с именем, равным «Представление Sea motel» или «Бюджета hotel», разделенные "|"):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Найти все гостиницы, где все комнаты иметь тег «wifi» или «ванная»:

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Найти совпадение для фразы в пределах коллекции, например «жаркие выкинуть стойках» или «hairdryer включены» в тегах.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Найти документы со словом waterfront. Этот запрос фильтрации идентичен [поисковому запросу](https://docs.microsoft.com/rest/api/searchservice/search-documents) с `search=waterfront`:

    $filter=search.ismatchscoring('waterfront')

Найти документы со словом hostel и рейтингом, большим или равным 4, или документы со словом motel и рейтингом 5. Этот запрос не может быть выражен без `search.ismatchscoring` работать, так как она объединяет компонент full-text search с помощью операции фильтрации с помощью `or`.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Найти документы без слова luxury.

    $filter=not search.ismatch('luxury')

Найти документы с фразой "ocean view" или рейтингом 5. Запрос `search.ismatchscoring` будет выполняться только по отношению к полям `HotelName` и `Description`. Документы, которые соответствуют только второе предложение логического сложения возвращается слишком--гостиницы с `Rating` равно 5. Эти документы будут возвращены с оценкой, равным нулю, чтобы сделать его очистки, что они не соответствует ни одному из оцененных части выражения.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Найти гостиницы, термины «отель» и «airport» которых не более пяти слов друг от друга в описании, и где все комнаты некурящих. В этом запросе используется [полный язык запросов Lucene](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Общие сведения о языках выражений OData для службы поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
