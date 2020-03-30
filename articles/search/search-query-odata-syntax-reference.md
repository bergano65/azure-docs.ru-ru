---
title: Ссылка синтаксиса выражения OData
titleSuffix: Azure Cognitive Search
description: Формальная спецификация грамматики и синтаксиса для выражений OData в запросах Azure Cognitive Search.
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
ms.openlocfilehash: f3422fd10e062ae87bc165491e0d01ac2b4943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793235"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Ссылка синтаксиса выражения OData для когнитивного поиска Azure

Azure Cognitive Search использует [выражения OData](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) в качестве параметров на протяжении всего API. Чаще всего для `$orderby` `$filter` параметров используются выражения OData. Эти выражения могут быть сложными, содержащими несколько положений, функций и операторов. Однако даже простые выражения OData, такие как пути свойств, используются во многих частях API Azure Cognitive Search REST. Например, выражения путей используются для обозначения подполей сложных полей повсюду в API, например при `$select` перечислении подполей в [подсказывающем,](index-add-suggesters.md) [функции скоринга,](index-add-scoring-profiles.md)параметра, или даже [наместа поиска в запросах Lucene.](query-lucene-syntax.md)

В этой статье описаны все эти формы выражений OData с использованием формальной грамматики. Существует также [интерактивная диаграмма,](#syntax-diagram) чтобы помочь визуально изучить грамматику.

## <a name="formal-grammar"></a>Формальная грамматика

Мы можем описать подмножество языка OData, поддерживаемого Azure Cognitive Search, с помощью грамматики EBNF[(Расширенная форма Backus-Naur).](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) Правила перечислены "сверху вниз", начиная с самых сложных выражений, и разбивая их на более примитивные выражения. В верхней части находятся правила грамматики, которые соответствуют определенным параметрам Azure Cognitive Search REST API:

- [`$filter`](search-query-odata-filter.md), определяется `filter_expression` правилом.
- [`$orderby`](search-query-odata-orderby.md), определяется `order_by_expression` правилом.
- [`$select`](search-query-odata-select.md), определяется `select_expression` правилом.
- Полевые пути, `field_path` определенные правилом. Полевые пути используются во всем API. Они могут относиться либо к полям верхнего уровня индекса, либо к подполям с одним или более [сложными](search-howto-complex-data-types.md) предками полей.

После EBNF представляет собой диаграмму [синтаксиса,](https://en.wikipedia.org/wiki/Syntax_diagram) которая позволяет интерактивно исследовать грамматику и взаимосвязи между ее правилами.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

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

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

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


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>Диаграмма синтаксиса

Чтобы визуально изучить грамматику языка OData, поддерживаемую Azure Cognitive Search, попробуйте интерактивную диаграмму синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>См. также  

- [Фильтры в когнитивном поиске Azure](search-filters.md)
- [Поиск документов &#40;Azure Когнитивный поиск REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Синтаксис запросов Lucene](query-lucene-syntax.md)
- [Простой синтаксис запроса в когнитивном поиске Azure](query-simple-syntax.md)
