---
title: Справочник по синтаксису выражений OData - поиска Azure
description: Формальный грамматике и синтаксисе спецификация OData выражения в запросах поиска Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: cccfb749af07d1deeeda6e94de9c2cd5ce5396f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079669"
---
# <a name="odata-expression-syntax-reference-for-azure-search"></a>Справочник по синтаксису выражений OData для службы поиска Azure

Поиск Azure использует [выражения OData](http://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) как параметры в API. Чаще всего выражения OData используются для `$orderby` и `$filter` параметров. Эти выражения могут быть сложными, содержащий несколько предложений, функции и операторы. Тем не менее даже простые выражения OData, такие как свойства пути, используемые во многих частях API REST службы поиска Azure. Например, выражения пути используются для ссылки на вложенные поля сложных полей в API, например когда список вложенные поля в любом файле [средство подбора](index-add-suggesters.md), [функция оценки](index-add-scoring-profiles.md), `$select` параметр , или даже [относящегося к полю поиска в запросах Lucene](query-lucene-syntax.md).

В этой статье описываются все эти формы с помощью формальная Грамматика выражений OData. Имеется также [интерактивной схемы](#syntax-diagram) для визуального изучения грамматики.

## <a name="formal-grammar"></a>Формальная Грамматика

Описывается то подмножество языка OData, поддерживаемые службой поиска Azure с помощью EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) грамматики. «Сверху вниз», начиная с самых сложных выражений и разбить их на более простые выражения перечислены правила. Вверху размещаются правил грамматики, соответствующие конкретным параметрам API REST службы поиска Azure:

- [`$filter`](search-query-odata-filter.md), определяемый `filter_expression` правило.
- [`$orderby`](search-query-odata-orderby.md), определяемый `order_by_expression` правило.
- [`$select`](search-query-odata-select.md), определяемый `select_expression` правило.
- Поле пути, определяется `field_path` правило. Поле пути используются API. Они могут ссылаться на поля индекса, либо верхнего уровня или вложенные поля с одним или несколькими [сложных поле](search-howto-complex-data-types.md) предков.

После просмотра EBNF [диаграмма синтаксиса](https://en.wikipedia.org/wiki/Syntax_diagram) , позволяющий интерактивно исследовать грамматики и связи между его правила.

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

Для визуального изучения грамматики языка OData, поддерживаемые службой поиска Azure, попробуйте интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>См. также  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
- [Синтаксис запросов Lucene](query-lucene-syntax.md)
- [Простой синтаксис запросов в службе "Поиск Azure"](query-simple-syntax.md)
