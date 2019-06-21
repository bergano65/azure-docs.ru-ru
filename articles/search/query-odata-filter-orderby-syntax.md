---
title: Общие сведения о языках OData - поиска Azure
description: Общие сведения о языке OData для фильтров, выберите и order-by для запросов поиска Azure.
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
ms.openlocfilehash: 166c23088fe0388199ca51efde05153bb5697e38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063705"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>Общие сведения о языке OData для `$filter`, `$orderby`, и `$select` в службе поиска Azure

Поиск Azure поддерживает подмножество выражений синтаксиса OData для **$filter**, **$orderby**, и **$select** выражения. Выражения для фильтрации вычисляются при синтаксическом анализе запроса. Они ограничивают поиск конкретными полями или добавляют критерии соответствия, используемые во время обработки индекса. Выражения Order by применяются как стадию завершающей обработки отказа результирующий набор, чтобы отсортировать документы, которые возвращаются. Выражения SELECT определяет, какие поля документа должны быть включены в результирующий набор. Синтаксис этих выражений отличается от [простой](query-simple-syntax.md) или [полный](query-lucene-syntax.md) синтаксис, который используется в запроса **поиска** параметра, несмотря на то, что некоторое перекрытие в синтаксисе ссылки на поля.

В этой статье описывается язык выражений OData, используемый в фильтрах, предложение order by и выражения select. Язык представлен «снизу вверх», начиная с самых базовых элементов и на их основе. В отдельной статье описан синтаксис верхнего уровня для каждого параметра:

- [синтаксис $filter](search-query-odata-filter.md)
- [синтаксис $orderby](search-query-odata-orderby.md)
- [$select syntax](search-query-odata-select.md)

OData выражения в диапазоне от простых до очень сложными, но они все совместно используют общие элементы. Самый простой части выражения OData в службе поиска Azure — это:

- **Поле пути**, которые относятся к определенной полей индекса.
- **Константы**, которые являются литеральные значения определенного типа данных.

> [!NOTE]
> Терминология в службе поиска Azure отличается от [стандарт OData](https://www.odata.org/documentation/) несколькими способами. Мы называем **поле** в службе поиска Azure называется **свойство** в OData и аналогично для **путь поля** и **путь к свойству**. **Индекс** содержащий **документов** в службе поиска Azure упоминается более общем случае в OData как **набор сущностей** содержащий **сущностей**. Терминология службы поиска Azure используется эта ссылка.

## <a name="field-paths"></a>Поле пути

Следующие EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику поля путей.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Также доступна схему интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> См. в разделе [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md) для завершения EBNF.

Путь к полю состоит из одного или нескольких **идентификаторы** разделенных косыми чертами. Каждый идентификатор — это последовательность символов, должен начинаться с буквы ASCII или знака подчеркивания и содержать только ASCII буквы, цифры или символы подчеркивания. Буквы могут быть в верхнем или нижнем регистре.

Идентификатор может ссылаться на имя поля, или чтобы **переменная диапазона** в контексте [выражения коллекции](search-query-odata-collection-operators.md) (`any` или `all`) в фильтре. Переменная диапазона доступна как переменная цикла, представляющий текущий элемент коллекции. Для сложных коллекций этой переменной представляет объект, поэтому можно использовать поле пути для ссылки на вложенные поля переменной. Это аналогично точечной нотации во многих языках программирования.

В следующей таблице показаны примеры путей к поля:

| Путь к полю | Описание |
| --- | --- |
| `HotelName` | Ссылается на поле верхнего уровня индекса |
| `Address/City` | Ссылается на `City` подзапроса поле сложных поля в индексе; `Address` имеет тип `Edm.ComplexType` в этом примере |
| `Rooms/Type` | Ссылается на `Type` подзапроса поле поля в индексе; `Rooms` имеет тип `Collection(Edm.ComplexType)` в этом примере |
| `Stores/Address/Country` | Ссылается на `Country` подзапроса поле `Address` подзапроса поле поля в индексе; `Stores` имеет тип `Collection(Edm.ComplexType)` и `Address` имеет тип `Edm.ComplexType` в этом примере |
| `room/Type` | Ссылается на `Type` подзапроса поле `room` переменной диапазона, например в критерии фильтра `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Ссылается на `Country` подзапроса поле `Address` подзапроса поле `store` переменной диапазона, например в критерии фильтра `Stores/any(store: store/Address/Country eq 'Canada')` |

Значение пути к полю отличается в зависимости от контекста. В фильтрах, путь к полю ссылается на значение *единственных* поля в текущем документе. В других контекстах например **$orderby**, **$select**, или в [относящегося к полю поиска в полный синтаксис Lucene](query-lucene-syntax.md#bkmk_fields), путь к полю ссылается на само поле. Это различие имеет некоторые последствия для использование поля путей в фильтрах.

Рассмотрим пути к полю `Address/City`. В фильтре он ссылается на один идентификатор города для текущего документа, например «Сан-Франциско». Напротив `Rooms/Type` ссылается на `Type` вложенные поля для многих комнаты (например, «стандартный» для первого места «deluxe» для второй помещений и т. д.). Так как `Rooms/Type` не относится к *единственных* вложенные поля `Type`, он не может использоваться непосредственно в фильтре. Вместо этого, чтобы отфильтровать тип номера, используется [лямбда-выражение](search-query-odata-collection-operators.md) с переменной диапазона, следующим образом:

    Rooms/any(room: room/Type eq 'deluxe')

В этом примере переменная диапазона `room` отображается в `room/Type` пути к полю. Таким образом, `room/Type` относится к типу текущего места в текущем документе. Это один экземпляр `Type` поля ввода, чтобы его можно использовать непосредственно в фильтре.

### <a name="using-field-paths"></a>С помощью поля путей

Поле пути используются в многочисленными параметрами [API службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/). В следующей таблице перечислены все места, где они могут использоваться, а также каких-либо ограничений на их использование:

| API | Имя параметра | Ограничения |
| --- | --- | --- |
| [Создание](https://docs.microsoft.com/rest/api/searchservice/create-index) или [обновления](https://docs.microsoft.com/rest/api/searchservice/update-index) индекса | `suggesters/sourceFields` | Нет |
| [Создание](https://docs.microsoft.com/rest/api/searchservice/create-index) или [обновления](https://docs.microsoft.com/rest/api/searchservice/update-index) индекса | `scoringProfiles/text/weights` | Может ссылаться только на **для поиска** поля |
| [Создание](https://docs.microsoft.com/rest/api/searchservice/create-index) или [обновления](https://docs.microsoft.com/rest/api/searchservice/update-index) индекса | `scoringProfiles/functions/fieldName` | Может ссылаться только на **фильтруемых** поля |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` Когда `queryType` — `full` | Может ссылаться только на **для поиска** поля |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Может ссылаться только на **аспектируемый** поля |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Может ссылаться только на **для поиска** поля |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Может ссылаться только на **для поиска** поля |
| [Предложить](https://docs.microsoft.com/rest/api/searchservice/suggestions) и [автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Можно ссылаться только на поля, которые являются частью [средства подбора](index-add-suggesters.md) |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents), [предложить](https://docs.microsoft.com/rest/api/searchservice/suggestions), и [автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Может ссылаться только на **фильтруемых** поля |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) и [предложить](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Может ссылаться только на **сортируемого** поля |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents), [предложить](https://docs.microsoft.com/rest/api/searchservice/suggestions), и [Уточняющий запрос](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Может ссылаться только на **извлекаемые** поля |

## <a name="constants"></a>Константы

Константы в OData — литеральные значения заданного [модели EDM](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) тип (модель EDM). См. в разделе [поддерживаемые типы данных](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) список поддерживаемых типов в службе поиска Azure. Константы типов коллекций не поддерживаются.

Ниже приведены примеры констант для каждого из типов данных, поддерживаемых службой поиска Azure:

| Тип данных | Константы |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

Следующие EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику для большинства из констант, показано в приведенной выше таблице. Грамматика для гео пространственных типов можно найти в [геопространственные функции OData в службе поиска Azure](search-query-odata-geo-spatial-functions.md).

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

Также доступна схему интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> См. в разделе [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md) для завершения EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Построение выражений из поля путей и константы

Поле пути и константы являются основной частью выражения OData, но они уже сами полного выражения. На самом деле **$select** параметр в службе поиска Azure — всего лишь список поля путей, разделенных запятыми и **$orderby** не сильно сложнее, чем **$select**. Если имеется поле типа `Edm.Boolean` в индексе, можно даже написать фильтр, который представляет путь этого поля. Константы `true` и `false` также содержатся допустимые фильтры.

Однако в большинстве случаев вам потребуется более сложные выражения, ссылающиеся на более чем одно поле и константы. Эти выражения строятся по-разному в зависимости от параметра.

Следующие EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику для **$filter**, **$orderby**, и **$select** параметров. Они создаются из более простые выражения, которые ссылаются на поле пути и константы:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Также доступна схему интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> См. в разделе [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md) для завершения EBNF.

**$Orderby** и **$select** параметры являются оба списка с разделителями запятыми из более простые выражения. **$Filter** параметр представляет собой логическое выражение, состоящий более простой вложенных выражений. Эти вложенные выражения объединяются с помощью логических операторов, таких как [ `and`, `or`, и `not` ](search-query-odata-logical-operators.md), операторы сравнения, такие как [ `eq`, `lt`, `gt`, и так далее](search-query-odata-comparison-operators.md)и коллекции операторы, такие как [ `any` и `all` ](search-query-odata-collection-operators.md).

**$Filter**, **$orderby**, и **$select** параметры рассматриваются более подробно в следующих статьях:

- [Синтаксис OData $filter в службе поиска Azure](search-query-odata-filter.md)
- [Синтаксис OData $orderby в службе поиска Azure](search-query-odata-orderby.md)
- [Синтаксис OData $select в службе поиска Azure](search-query-odata-select.md)

## <a name="see-also"></a>См. также  

- [Фасетная навигация в службе поиска Azure](search-faceted-navigation.md)
- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
- [Синтаксис запросов Lucene](query-lucene-syntax.md)
- [Простой синтаксис запросов в службе "Поиск Azure"](query-simple-syntax.md)
