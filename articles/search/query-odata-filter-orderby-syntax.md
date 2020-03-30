---
title: Обзор языка OData
titleSuffix: Azure Cognitive Search
description: Обзор языка OData для фильтров, выбора и заказа для запросов Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: f3a1be435e297ab4a9ba7f8bfbd5f3ce3451d8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153882"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Обзор языка OData `$filter` `$orderby`для, `$select` и в Azure Когнитивный поиск

Azure Cognitive Search поддерживает подмножество синтаксиса выражения OData для **выражения $filter,** **$orderby**и **$select** выражений. Выражения для фильтрации вычисляются при синтаксическом анализе запроса. Они ограничивают поиск конкретными полями или добавляют критерии соответствия, используемые во время обработки индекса. Выражения заказа применяются как шаг после обработки по сравнению с результатом, установленным для сортировки возвращенных документов. Выберите выражения, определяющие, какие поля документов включены в набор результатов. Синтаксис этих выражений отличается от [простого](query-simple-syntax.md) или [полного](query-lucene-syntax.md) синтаксиса запроса, который используется в параметре **поиска,** хотя в синтаксисе есть некоторое совпадение для ссылок.

В этой статье содержится обзор языка выражения OData, используемого в фильтрах, упорядочении и выборах. Язык представлен «снизу вверх», начиная с самых элементарных элементов и опираясь на них. Синтаксис верхнего уровня для каждого параметра описан в отдельной статье:

- [$filter синтаксис](search-query-odata-filter.md)
- [$orderby синтаксис](search-query-odata-orderby.md)
- [$select синтаксис](search-query-odata-select.md)

Выражения OData варьируются от простых до очень сложных, но все они имеют общие элементы. Наиболее основными частями выражения OData в Azure Cognitive Search являются:

- **Полевые пути,** которые относятся к определенным полям вашего индекса.
- **Константы,** которые являются буквальнозначениями определенного типа данных.

> [!NOTE]
> Терминология в когнитивном поиске Azure отличается от [стандарта OData](https://www.odata.org/documentation/) несколькими способами. То, что мы называем **полем** в Azure Cognitive Search, называется **свойством** в OData и аналогично для **полевых путей** по сравнению с **траекторией свойства.** **Индекс,** содержащий **документы** в Azure Cognitive Search, в более общем плане упоминается в OData как **набор сущностей,** содержащий **сущности.** В этой ссылке используется терминология когнитивного поиска Azure.

## <a name="field-paths"></a>Полевые дорожки

Следующие EBNF ([Расширенная форма Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику полевых путей.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Также доступна интерактивная диаграмма синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Смотрите [ссылку синтаксиса выражения OData для Azure Cognitive Search](search-query-odata-syntax-reference.md) для полного EBNF.

Полевый путь состоит из одного или нескольких **идентификаторов,** разделенных слэшами. Каждый идентификатор представляет собой последовательность символов, которые должны начинаться с письма ASCII или подчеркивать, и содержат только ASCII буквы, цифры или подчеркнутые. Буквы могут быть верхнего или нижнего корпуса.

Идентификатор может относиться либо к имени поля, либо к **переменной** `all`диапазона в контексте выражения [коллекции](search-query-odata-collection-operators.md) (или)`any` в фильтре. Переменная диапазона подобна переменной цикла, представляющей текущий элемент коллекции. Для сложных коллекций эта переменная представляет объект, поэтому можно использовать полевые пути для обозначения подполей переменной. Это аналогично обозначению точек на многих языках программирования.

Примеры полевых путей приведены в следующей таблице:

| Полевый путь | Описание |
| --- | --- |
| `HotelName` | Относится к области верхнего уровня индекса |
| `Address/City` | Относится `City` к подпою сложного поля в индексе; `Address` тип `Edm.ComplexType` в этом примере |
| `Rooms/Type` | Относится `Type` к подпою сложного поля сбора в индексе; `Rooms` тип `Collection(Edm.ComplexType)` в этом примере |
| `Stores/Address/Country` | В индексе `Country` речь идет `Address` о подполе подполя сложного поля сбора в индексе; `Stores` тип `Collection(Edm.ComplexType)` и `Address` тип `Edm.ComplexType` в этом примере |
| `room/Type` | Относится `Type` к подполе `room` переменной диапазона, например, в выражении фильтра`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Относится `Country` к подполе `Address` подполя переменной `store` диапазона, например, в выражении фильтра`Stores/any(store: store/Address/Country eq 'Canada')` |

Значение пути поля отличается в зависимости от контекста. В фильтрах путь поля относится к значению *одного экземпляра* поля в текущем документе. В других контекстах, таких как **$orderby,** **$select,** или в [полевом поиске в полном синтаксисе Lucene,](query-lucene-syntax.md#bkmk_fields)поле путь относится к самому полю. Эта разница имеет некоторые последствия для использования полевых путей в фильтрах.

Рассмотрим путь `Address/City`поля. В фильтре это относится к одному городу для текущего документа, как "Сан-Франциско". В отличие `Rooms/Type` от `Type` этого, относится к подпою для многих номеров (например, "стандарт" для первого номера, "люкс" для второго номера, и так далее). Поскольку `Rooms/Type` не относится к *одному экземпляру* подполя, `Type`он не может быть использован непосредственно в фильтре. Вместо этого, чтобы отфильтровать тип комнаты, вы бы использовали [выражение lambda](search-query-odata-collection-operators.md) с переменной диапазона, как это:

    Rooms/any(room: room/Type eq 'deluxe')

В этом примере `room` переменная `room/Type` диапазона отображается в поле. Таким образом, `room/Type` относится к типу текущей комнаты в текущем документе. Это единичная инстанция `Type` подполя, поэтому его можно использовать непосредственно в фильтре.

### <a name="using-field-paths"></a>Использование полевых путей

Полевые пути используются во многих параметрах [ApIs Azure Cognitive Search REST.](https://docs.microsoft.com/rest/api/searchservice/) В следующей таблице перечислены все места, где они могут быть использованы, а также любые ограничения на их использование:

| API | Имя параметра | Ограничения |
| --- | --- | --- |
| [Создание](https://docs.microsoft.com/rest/api/searchservice/create-index) или [обновление индекса](https://docs.microsoft.com/rest/api/searchservice/update-index) | `suggesters/sourceFields` | None |
| [Создание](https://docs.microsoft.com/rest/api/searchservice/create-index) или [обновление индекса](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/text/weights` | Можно ссылаться только на **поля поиска** |
| [Создание](https://docs.microsoft.com/rest/api/searchservice/create-index) или [обновление индекса](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/functions/fieldName` | Может относиться только к **фильтруемым полям** |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`когда `queryType` это`full` | Можно ссылаться только на **поля поиска** |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Может относиться только к **facetable** поля |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Можно ссылаться только на **поля поиска** |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Можно ссылаться только на **поля поиска** |
| [Предложить](https://docs.microsoft.com/rest/api/searchservice/suggestions) и [автозаполнить](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Может ссылаться только на поля, являющиеся частью средства [подбора](index-add-suggesters.md) |
| [Поиск,](https://docs.microsoft.com/rest/api/searchservice/search-documents) [Предложение,](https://docs.microsoft.com/rest/api/searchservice/suggestions)и [Автозаполнение](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Может относиться только к **фильтруемым полям** |
| [Поиск](https://docs.microsoft.com/rest/api/searchservice/search-documents) и [предложение](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Может относиться только к **сортируемым полям** |
| [Поиск,](https://docs.microsoft.com/rest/api/searchservice/search-documents) [Предложение](https://docs.microsoft.com/rest/api/searchservice/suggestions), и [поиск](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Может относиться только к **извлекаемым полям** |

## <a name="constants"></a>Константы

Константы в OData являются буквальными значениями данного типа [модели данных entity](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) Data (EDM). Смотрите [поддерживаемые типы данных](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) для списка поддерживаемых типов в Azure Cognitive Search. Константы типов коллекций не поддерживаются.

В следующей таблице приведены примеры констант для каждого из типов данных, поддерживаемых Azure Cognitive Search:

| Тип данных | Пример константы |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Спасаясь от специальных персонажей в константах строки

Строковые константы в OData разграничены отдельными котировками. Если вам нужно построить запрос с константой строки, которая сама по себе может содержать одиночные котировки, вы можете избежать встроенных кавычек, удвоив их.

Например, фраза с неформатизированным апострофом, как "автомобиль Алисы" будет `'Alice''s car'`представлена в OData как строка постоянной .

> [!IMPORTANT]
> При построении фильтров программно, важно помнить, чтобы избежать строки константы, которые приходят от пользовательского ввода. Это делается для уменьшения возможности [атак впрыска,](https://wikipedia.org/wiki/SQL_injection)особенно при использовании фильтров для [реализации обрезки безопасности.](search-security-trimming-for-azure-search.md)

### <a name="constants-syntax"></a>Синтаксис константа

Следующие EBNF ([Расширенная форма Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику для большинства констант, показанных в приведенной выше таблице. Грамматику для геопространственных типов можно найти в [геопространственных функциях OData в Azure Cognitive Search.](search-query-odata-geo-spatial-functions.md)

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

Также доступна интерактивная диаграмма синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Смотрите [ссылку синтаксиса выражения OData для Azure Cognitive Search](search-query-odata-syntax-reference.md) для полного EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Создание выражений из полевых путей и констант

Полевые пути и константы являются основной частью выражения OData, но они уже сами по себе являются полными выражениями. На самом деле, **$select** параметр в Azure Cognitive Search является не чем иным, как запятой разделенных список полевых путей, и **$orderby** не намного сложнее, чем **$select**. Если в индексе есть `Edm.Boolean` поле типа, можно даже написать фильтр, который является не чем иным, как траекторией этого поля. `true` Константы `false` и также действительные фильтры.

Тем не менее, большую часть времени вам понадобятся более сложные выражения, которые относятся к более чем одному полю и констант. Эти выражения строятся по-разному в зависимости от параметра.

Следующие EBNF[(Расширенная форма Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику для **$filter,** **$orderby**и **$select** параметров. Они построены из более простых выражений, которые относятся к полевым путям и константам:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Также доступна интерактивная диаграмма синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Смотрите [ссылку синтаксиса выражения OData для Azure Cognitive Search](search-query-odata-syntax-reference.md) для полного EBNF.

**Параметры $orderby** и **$select** являются как запятыми, разделенными списками более простых выражений. Параметр **$filter** является выражением Boolean, которое состоит из более простых субвыражений. Эти подвыражения объединяются с использованием логических операторов, таких как [ `and`, `or`и, `not` ](search-query-odata-logical-operators.md)сравнительные операторы, такие как [ `eq`, `lt` `gt`, и так далее,](search-query-odata-comparison-operators.md)и операторы сбора, такие как [ `any` и `all` ](search-query-odata-collection-operators.md).

**Параметры $filter,** **$orderby**и **$select** более подробно рассматриваются в следующих статьях:

- [OData $filter синтаксис в когнитивном поиске Azure](search-query-odata-filter.md)
- [OData $orderby синтаксис в когнитивном поиске Azure](search-query-odata-orderby.md)
- [OData $select синтаксис в когнитивном поиске Azure](search-query-odata-select.md)

## <a name="see-also"></a>См. также  

- [Граненая навигация в azure Cognitive Search](search-faceted-navigation.md)
- [Фильтры в когнитивном поиске Azure](search-filters.md)
- [Поиск документов &#40;Azure Когнитивный поиск REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Синтаксис запросов Lucene](query-lucene-syntax.md)
- [Простой синтаксис запроса в когнитивном поиске Azure](query-simple-syntax.md)
