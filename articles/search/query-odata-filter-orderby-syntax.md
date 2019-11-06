---
title: Обзор языка OData
titleSuffix: Azure Cognitive Search
description: Общие сведения о языке OData для запросов Azure Когнитивный поиск.
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
ms.openlocfilehash: e0db41098287ff011416932a0d44a1cb9f76127d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786157"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Общие сведения о языке OData для `$filter`, `$orderby`и `$select` в Azure Когнитивный поиск

Когнитивный поиск Azure поддерживает подмножество синтаксиса выражений OData для выражений **$Filter**, **$OrderBy**и **$SELECT** . Выражения для фильтрации вычисляются при синтаксическом анализе запроса. Они ограничивают поиск конкретными полями или добавляют критерии соответствия, используемые во время обработки индекса. Выражения ORDER-BY применяются в качестве прохода после обработки результирующего набора для сортировки возвращаемых документов. Выбор выражений определяет, какие поля документа включаются в результирующий набор. Синтаксис этих выражений отличается от синтаксиса [простого](query-simple-syntax.md) или [полного](query-lucene-syntax.md) запроса, используемого в параметре **поиска** , хотя существует некоторое перекрытие синтаксиса для ссылок на поля.

В этой статье представлен обзор языка выражений OData, используемого в выражениях Filters, ORDER-BY и SELECT. Язык представлен "снизу вверх", начиная с самых базовых элементов и заканчивая их созданием. Синтаксис верхнего уровня для каждого параметра описан в отдельной статье:

- [синтаксис $filter](search-query-odata-filter.md)
- [синтаксис $orderby](search-query-odata-orderby.md)
- [синтаксис $select](search-query-odata-select.md)

Выражения OData находятся в диапазоне от простых до очень сложных, но все они совместно используют общие элементы. Ниже перечислены основные части выражения OData в Azure Когнитивный поиск.

- **Пути к полям**, которые ссылаются на определенные поля индекса.
- **Константы**, являющиеся литеральными значениями определенного типа данных.

> [!NOTE]
> Терминология в Azure Когнитивный поиск отличается от [стандарта OData](https://www.odata.org/documentation/) несколькими способами. Вызов **поля** в Azure когнитивный Поиск называется **свойством** в OData и аналогичным образом для **пути поля** и **пути к свойству**. **Индекс** , содержащий **документы** в когнитивный Поиск Azure, часто упоминается в OData как **набор сущностей** , содержащий **сущности**. Терминология Azure Когнитивный поиск используется по всей этой ссылке.

## <a name="field-paths"></a>Пути к полям

Следующая EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику путей к полям.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Доступна также интерактивная схема синтаксиса:

> [!div class="nextstepaction"]
> [Схема синтаксиса OData для Когнитивный поиск Azure](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для Azure когнитивный Поиск](search-query-odata-syntax-reference.md) .

Путь к полю состоит из одного или нескольких **идентификаторов** , разделенных косыми чертами. Каждый идентификатор представляет собой последовательность символов, которая должна начинаться с буквы ASCII или знака подчеркивания и содержать только буквы ASCII, цифры и символы подчеркивания. Буквы могут быть в верхнем или нижнем регистре.

Идентификатор может ссылаться либо на имя поля, либо на **переменную диапазона** в контексте [выражения коллекции](search-query-odata-collection-operators.md) (`any` или `all`) в фильтре. Переменная диапазона похожа на переменную цикла, которая представляет текущий элемент коллекции. Для сложных коллекций эта переменная представляет объект, поэтому можно использовать пути полей для ссылки на подполя переменной. Это аналогично точечной нотации во многих языках программирования.

Примеры путей к полям приведены в следующей таблице.

| Путь к полю | Описание |
| --- | --- |
| `HotelName` | Ссылается на поле индекса верхнего уровня |
| `Address/City` | Относится к `City` подполе сложного поля в индексе; в этом примере `Address` имеет тип `Edm.ComplexType` |
| `Rooms/Type` | Ссылается на подполе `Type` поля сложной коллекции в индексе; в этом примере `Rooms` имеет тип `Collection(Edm.ComplexType)` |
| `Stores/Address/Country` | Относится к вложенному полю `Country` поля `Address` сложной коллекции в индексе. `Stores` имеет тип `Collection(Edm.ComplexType)`, а `Address` имеет тип `Edm.ComplexType` в этом примере |
| `room/Type` | Относится к подполю `Type` переменной диапазона `room`, например в критерии фильтра `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Относится к под`Country` подполя `Address` подполя `store` переменной диапазона, например в критерии фильтра `Stores/any(store: store/Address/Country eq 'Canada')` |

Значение пути к полю различается в зависимости от контекста. В фильтрах поле пути к полю относится к значению *одного экземпляра* поля в текущем документе. В других контекстах, таких как **$OrderBy**, **$SELECT**или в [поле поиска в полном синтаксисе Lucene](query-lucene-syntax.md#bkmk_fields), путь к полю относится к самому полю. Это различие имеет некоторые последствия использования путей к полям в фильтрах.

Рассмотрим путь поля `Address/City`. В фильтре это относится к одному городу для текущего документа, например "Сан-Франциско". Напротив, `Rooms/Type` относится к вспомогательному полю `Type` для многих комнат (например, "Стандартный" для первой комнаты, "Deluxe" для второй комнаты и т. д.). Поскольку `Rooms/Type` не ссылается на *единственный экземпляр* `Type`подполя, его нельзя использовать непосредственно в фильтре. Вместо этого для фильтрации по типу комнаты можно использовать [лямбда-выражение](search-query-odata-collection-operators.md) с переменной диапазона, например:

    Rooms/any(room: room/Type eq 'deluxe')

В этом примере переменная диапазона `room` отображается в пути к полю `room/Type`. Таким образом, `room/Type` ссылается на тип текущей комнаты в текущем документе. Это единственный экземпляр подполя `Type`, поэтому его можно использовать непосредственно в фильтре.

### <a name="using-field-paths"></a>Использование путей к полям

Пути к полям используются во многих параметрах [интерфейсов API службы Azure когнитивный Поиск](https://docs.microsoft.com/rest/api/searchservice/). В следующей таблице перечислены все места, где их можно использовать, а также все ограничения на их использование.

| API | Имя параметра | Ограничения |
| --- | --- | --- |
| [Создать](https://docs.microsoft.com/rest/api/searchservice/create-index) или [Обновить](https://docs.microsoft.com/rest/api/searchservice/update-index) индекс | `suggesters/sourceFields` | Отсутствуют |
| [Создать](https://docs.microsoft.com/rest/api/searchservice/create-index) или [Обновить](https://docs.microsoft.com/rest/api/searchservice/update-index) индекс | `scoringProfiles/text/weights` | Может ссылаться только на поля с **возможностью поиска** |
| [Создать](https://docs.microsoft.com/rest/api/searchservice/create-index) или [Обновить](https://docs.microsoft.com/rest/api/searchservice/update-index) индекс | `scoringProfiles/functions/fieldName` | Может ссылаться только на **фильтруемые** поля |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`, когда `queryType` `full` | Может ссылаться только на поля с **возможностью поиска** |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Может ссылаться только на поля с **аспектами** |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Может ссылаться только на поля с **возможностью поиска** |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Может ссылаться только на поля с **возможностью поиска** |
| [Предложение](https://docs.microsoft.com/rest/api/searchservice/suggestions) и [Автозаполнение](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Может ссылаться только на поля, являющиеся частью средства [подбора](index-add-suggesters.md) |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents), [предложение](https://docs.microsoft.com/rest/api/searchservice/suggestions)и [Автозаполнение](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Может ссылаться только на **фильтруемые** поля |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) и [предложение](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Может ссылаться только на поля, доступные для **сортировки** |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents), [предложение](https://docs.microsoft.com/rest/api/searchservice/suggestions)и [Поиск](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Может ссылаться только на **извлекаемые** поля |

## <a name="constants"></a>Постоянным

Константы в OData — это литеральные значения заданного типа [EDM](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM). Список поддерживаемых типов см. в разделе [Поддерживаемые типы данных](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) когнитивный Поиск Azure. Константы типов коллекций не поддерживаются.

В следующей таблице приведены примеры констант для каждого из типов данных, поддерживаемых Когнитивный поиск Azure.

| Тип данных | Примеры констант |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

Следующая EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику для большинства констант, показанных в приведенной выше таблице. Грамматику для геопространственных типов можно найти в [геопространственных функциях OData в Azure когнитивный Поиск](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Доступна также интерактивная схема синтаксиса:

> [!div class="nextstepaction"]
> [Схема синтаксиса OData для Когнитивный поиск Azure](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для Azure когнитивный Поиск](search-query-odata-syntax-reference.md) .

## <a name="building-expressions-from-field-paths-and-constants"></a>Создание выражений из путей и констант полей

Пути к полям и константы — это основная часть выражения OData, но они уже являются полными выражениями. Фактически, параметр **$SELECT** в Azure когнитивный Поиск имеет значение Nothing, но разделенный запятыми список путей к полям, а **$OrderBy** не намного сложнее, чем **$SELECT**. Если в индексе имеется поле типа `Edm.Boolean`, можно даже написать фильтр, не имеющий пути к этому полю. Константы `true` и `false` также являются допустимыми фильтрами.

Однако в большинстве случаев требуются более сложные выражения, ссылающиеся на более чем одно поле и константу. Эти выражения создаются различными способами в зависимости от параметра.

Следующая EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику для параметров **$Filter**, **$OrderBy**и **$SELECT** . Они основаны на более простых выражениях, которые ссылаются на пути к полям и константы:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Доступна также интерактивная схема синтаксиса:

> [!div class="nextstepaction"]
> [Схема синтаксиса OData для Когнитивный поиск Azure](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для Azure когнитивный Поиск](search-query-odata-syntax-reference.md) .

Параметры **$OrderBy** и **$SELECT** являются разделенными запятыми списками более простых выражений. Параметр **$Filter** является логическим выражением, состоящим из более простых подвыражений. Эти вложенные выражения комбинируются с помощью логических операторов, таких как [`and`, `or`и `not`](search-query-odata-logical-operators.md), таких операторов сравнения, как [`eq`, `lt`, `gt`и т. д](search-query-odata-comparison-operators.md)., а также операторов коллекций, таких как [`any` и `all`](search-query-odata-collection-operators.md).

Параметры **$Filter**, **$OrderBy**и **$SELECT** подробно рассматриваются в следующих статьях:

- [Синтаксис $filter OData в Azure Когнитивный поиск](search-query-odata-filter.md)
- [Синтаксис $orderby OData в Azure Когнитивный поиск](search-query-odata-orderby.md)
- [Синтаксис $select OData в Azure Когнитивный поиск](search-query-odata-select.md)

## <a name="see-also"></a>См. также  

- [Навигация с аспектами в Azure Когнитивный поиск](search-faceted-navigation.md)
- [Фильтры в Когнитивный поиск Azure](search-filters.md)
- [Поиск документов &#40;Когнитивный поиск Azure REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Синтаксис запросов Lucene](query-lucene-syntax.md)
- [Простой синтаксис запросов в Azure Когнитивный поиск](query-simple-syntax.md)
