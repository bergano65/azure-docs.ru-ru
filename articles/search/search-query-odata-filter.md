---
title: Ссылка на фильтр OData
titleSuffix: Azure Cognitive Search
description: Ссылка на язык OData и полный синтаксис, используемый для создания экспрессий фильтров в запросах Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282891"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>OData $filter синтаксис в когнитивном поиске Azure

Azure Cognitive Search использует [выражения фильтра OData](query-odata-filter-orderby-syntax.md) для применения дополнительных критериев к поисковому запросу, помимо полнотекстовых поисковых терминов. В этой статье подробно описывается синтаксис фильтров. Более подробную информацию о том, что такое фильтры и как их использовать для реализации конкретных сценариев запросов, можно найти [в Azure Cognitive Search.](search-filters.md)

## <a name="syntax"></a>Синтаксис

Фильтр на языке OData является выражением Boolean, которое, в свою очередь, может быть одним из нескольких типов выражения, как показано на следующем EBNF ([Расширенная форма Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

Также доступна интерактивная диаграмма синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Смотрите [ссылку синтаксиса выражения OData для Azure Cognitive Search](search-query-odata-syntax-reference.md) для полного EBNF.

Типы выражений булеана включают в себя:

- Выражения фильтра `any` коллекции с помощью или `all`. Они применяют критерии фильтрации к полям сбора. Для получения дополнительной информации смотрите [операторы сбора OData в Azure Cognitive Search](search-query-odata-collection-operators.md).
- Логические выражения, которые сочетают в `and`себе `or`другие выражения Boolean с помощью операторов , и `not`. Для получения дополнительной информации смотрите [логические операторы OData в Azure Cognitive Search](search-query-odata-logical-operators.md).
- Выражения сравнения, которые сравнивают поля или переменные диапазона `eq` `ne`с `gt` `lt`постоянными значениями с помощью операторов, `ge`, и `le`. Для получения дополнительной информации смотрите [операторы сравнения OData в Azure Cognitive Search](search-query-odata-comparison-operators.md). Сравнение выражений также используется для сравнения расстояний между геопространственных координат с помощью функции. `geo.distance` Для получения дополнительной информации смотрите [геопространственные функции OData в Azure Cognitive Search.](search-query-odata-geo-spatial-functions.md)
- Boolean буквальные `true` и `false`. Эти константы могут быть полезны иногда при программном генерации фильтров, но в противном случае не имеют тенденции использоваться на практике.
- Звонки на функции Boolean, в том числе:
  - `geo.intersects`, который проверяет, находится ли данный пункт в пределах данного полигона. Для получения дополнительной информации смотрите [геопространственные функции OData в Azure Cognitive Search.](search-query-odata-geo-spatial-functions.md)
  - `search.in`, который сравнивает переменную поля или диапазона с каждым значением в списке значений. Для получения дополнительной информации [см. `search.in` ](search-query-odata-search-in-function.md)
  - `search.ismatch`и `search.ismatchscoring`, которые выполняют полнотекстовые поисковые операции в контексте фильтра. Для получения дополнительной информации смотрите [функции полнотекстовых поиска OData в Azure Cognitive Search](search-query-odata-full-text-search-functions.md).
- Полевые пути или переменные типа. `Edm.Boolean` Например, если в индексе называется `IsEnabled` поле Boolean и вы `true`хотите вернуть все документы, `IsEnabled`где находится это поле, выражение фильтра может быть просто именем.
- Булин выражений в скобках. Использование скобки может помочь точно определить порядок операций в фильтре. Для получения дополнительной информации о приоритете по умолчанию операторов OData, см.

### <a name="operator-precedence-in-filters"></a>Приоритет оператора в фильтрах

Если вы пишете выражение фильтра без скобки вокруг его субвыражений, Azure Cognitive Search будет оценивать его в соответствии с набором правил приоритета оператора. Эти правила основаны на том, какие операторы используются для объединения субвыражений. В следующей таблице перечислены группы операторов по порядку от самого высокого к самому низкому приоритету:

| Группа | Оператор (ы) |
| --- | --- |
| Логические операторы | `not` |
| Операторы сравнения | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Логические операторы | `and` |
| Логические операторы | `or` |

Оператор, который выше в приведенной выше таблице, будет "связываться более плотно" к своим работам, чем другие операторы. Например, `and` имеет более высокий `or`приоритет, чем , и операторы сравнения имеют более высокий приоритет, чем любой из них, поэтому следующие два выражения эквивалентны:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

Оператор `not` имеет самый высокий приоритет из всех - даже выше, чем операторы сравнения. Вот почему, если вы попытаетесь написать фильтр, как это:

    not Rating gt 5

Вы получите сообщение об ошибке:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Эта ошибка происходит потому, что `Rating` оператор связан только `Edm.Int32`с полем, которое имеет тип, а не со всем выражением сравнения. Исправление заключается в том, чтобы положить операнд `not` в скобках:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Ограничения на размер выражений фильтрации

Размер и сложность выражений фильтра ограничены, которые можно отправить в Azure Cognitive Search. Ограничения ориентировочно основаны на количестве предложений в выражении фильтрации. Хорошим ориентиром является то, что если у вас есть сотни положений, вы рискуете превысить предел. Мы рекомендуем проектировать приложение таким образом, чтобы оно не генерировало фильтры неограниченного размера.

> [!TIP]
> Использование [ `search.in` функции](search-query-odata-search-in-function.md) вместо длительных разносок сравнений равенства может помочь избежать ограничения положения о фильтре, так как вызов функции считается единым положением.

## <a name="examples"></a>Примеры

Найти все отели, по крайней мере один номер с базовой ставкой менее $ 200, которые оцениваются в или выше 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Найти все отели, кроме "Морской вид Мотель", которые были отремонтированы с 2010 года:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Найти все отели, которые были отремонтированы в 2010 году или позже. В буквальном времени даты содержится информация о часовом поясе для Тихоокеанского стандартного времени:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Найти все отели, которые имеют парковку включены и где все номера не курят:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- OR -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Найти все отели класса люкс или отели с рейтингом 5, в которых есть парковка:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Найти все отели с тегом "Wi-Fi" по крайней мере в `Collection(Edm.String)` одном номере (где каждый номер имеет теги хранятся в поле):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Найти все отели с любыми номерами:  

    $filter=Rooms/any()

Найти все отели, которые не имеют номеров:

    $filter=not Rooms/any()

Найти все отели в пределах 10 `Location` километров от данной точки отсчета (где поле типа): `Edm.GeographyPoint`

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Найти все отели в пределах данного viewport `Location` описывается как полигон (где поле типа Edm.GeographyPoint). Полигон должен быть закрыт, то есть первый и последний наборы точек должны быть одинаковыми. Кроме того, [точки должны быть перечислены в порядке против часовой стрелки.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Найдите все отели, где поле «Описание» недействительно. Поле будет недействительным, если оно никогда не было установлено, или если оно было явно установлено в нулевых:  

    $filter=Description eq null

Найдите все отели с названием, равным мотеле «Морской вид», либо «Бюджетному отелю». Эти фразы содержат пробелы, а пространство является делимитетом по умолчанию. Можно указать альтернативный делимитер в одной кавыки в качестве третьего параметра строки:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Найдите все отели с названием, равным мотеле «Морской вид», либо «Бюджетному отелю», разделенным «Кью»):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Найти все отели, где все номера имеют тег "Wi-Fi" или "труба":

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Найдите совпадение по фразам в коллекции, таким как «нагретые стеллажи для полотенец» или «включаемые феном» в теги.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Найти документы со словом waterfront. Этот запрос фильтрации идентичен [поисковому запросу](https://docs.microsoft.com/rest/api/searchservice/search-documents) с `search=waterfront`:

    $filter=search.ismatchscoring('waterfront')

Найти документы со словом hostel и рейтингом, большим или равным 4, или документы со словом motel и рейтингом 5. Этот запрос не может быть `search.ismatchscoring` выражен без функции, так как `or`он сочетает в себе полнотекстовый поиск с использованием фильтра.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Найти документы без слова luxury.

    $filter=not search.ismatch('luxury')

Найти документы с фразой "ocean view" или рейтингом 5. Запрос `search.ismatchscoring` будет выполняться только по отношению к полям `HotelName` и `Description`. Документы, которые соответствовали только второму пункту запрета, `Rating` также будут возвращены - отели с равней 5. Эти документы будут возвращены со счетом, равным нулю, чтобы было ясно, что они не соответствовали ни одной из забитых частей выражения.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Найдите отели, где термины «отель» и «аэропорт» не более пяти слов в описании, и где все номера не курят. В этом запросе используется [полный язык запросов Lucene](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в когнитивном поиске Azure](search-filters.md)
- [Обзор языка выражения OData для когнитивного поиска Azure](query-odata-filter-orderby-syntax.md)
- [Ссылка синтаксиса выражения OData для когнитивного поиска Azure](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Azure Когнитивный поиск REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
