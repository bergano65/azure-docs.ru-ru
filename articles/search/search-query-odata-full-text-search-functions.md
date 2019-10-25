---
title: Справочник по функциям полнотекстового поиска OData
titleSuffix: Azure Cognitive Search
description: Функции полнотекстового поиска OData, Search. gettext и Search. исматчскоринг в запросах Azure Когнитивный поиск.
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
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793347"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Функции полнотекстового поиска OData в Azure Когнитивный поиск — `search.ismatch` и `search.ismatchscoring`

Azure Когнитивный поиск поддерживает полнотекстовый поиск в контексте [выражений фильтра OData](query-odata-filter-orderby-syntax.md) с помощью функций `search.ismatch` и `search.ismatchscoring`. Эти функции позволяют объединять полнотекстовый поиск с помощью исключительной логической фильтрации, так как это невозможно только с помощью параметра `search` верхнего уровня [API поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> Функции `search.ismatch` и `search.ismatchscoring` поддерживаются только в фильтрах в [API поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents). Они не поддерживаются в интерфейсах API [предложения](https://docs.microsoft.com/rest/api/searchservice/suggestions) или [автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Синтаксис

Следующая EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику функций `search.ismatch` и `search.ismatchscoring`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Доступна также интерактивная схема синтаксиса:

> [!div class="nextstepaction"]
> [Схема синтаксиса OData для Когнитивный поиск Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для Azure когнитивный Поиск](search-query-odata-syntax-reference.md) .

### <a name="searchismatch"></a>Поиск. Match

Функция `search.ismatch` вычисляет полнотекстовый поисковый запрос как часть критерия фильтра. Документы, соответствующие поисковому запросу, будут возвращены в результирующем наборе. Доступны следующие перегрузки этой функции:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Параметры определены в следующей таблице.

| Имя параметра | Тип | Описание |
| --- | --- | --- |
| `search` | `Edm.String` | Поисковый запрос (как [простой](query-simple-syntax.md) , так и [полный](query-lucene-syntax.md) синтаксис запроса Lucene). |
| `searchFields` | `Edm.String` | Разделенный запятыми список полей с возможностью поиска, в которых выполняется поиск; по умолчанию для всех полей, поддерживающих поиск, в индексе. При использовании [поиска по полю](query-lucene-syntax.md#bkmk_fields) в параметре `search` описатели полей в запросе Lucene переопределяют все поля, указанные в этом параметре. |
| `queryType` | `Edm.String` | `'simple'` или `'full'`; значение по умолчанию — `'simple'`. Указывает, какой язык запроса использовался в параметре `search`. |
| `searchMode` | `Edm.String` | `'any'` или `'all'`значение по умолчанию равно `'any'`. Указывает, должны ли быть сопоставлены все или все условия поиска в параметре `search`, чтобы подсчитать документ в соответствие. При использовании [логических операторов Lucene](query-lucene-syntax.md#bkmk_boolean) в параметре `search` они будут иметь приоритет над этим параметром. |

Все указанные выше параметры эквивалентны соответствующим [параметрам поискового запроса в API поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Функция `search.ismatch` возвращает значение типа `Edm.Boolean`, которое позволяет составить его с другими подвыражениями фильтра, используя логические [логические операторы](search-query-odata-logical-operators.md).

> [!NOTE]
> Когнитивный поиск Azure не поддерживает использование `search.ismatch` или `search.ismatchscoring` внутри лямбда-выражений. Это означает, что невозможно записать фильтры для коллекций объектов, которые могут сопоставлять полнотекстовые поисковые совпадения с ограниченным совпадением фильтра для одного и того же объекта. Дополнительные сведения об этом ограничении, а также примеры см. [в разделе Устранение неполадок фильтров сбора в Azure когнитивный Поиск](search-query-troubleshoot-collection-filters.md). Более подробные сведения о том, почему это ограничение существует, см. в разделе [Основные сведения о фильтрах коллекции в когнитивный Поиск Azure](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Поиск. исматчскоринг

Функция `search.ismatchscoring`, как и функция `search.ismatch`, возвращает `true` для документов, которые соответствуют запросу полнотекстового поиска, переданному в качестве параметра. Разница между ними заключается в том, что оценка релевантности документов, соответствующих запросу функции `search.ismatchscoring`, будет влиять на общую оценку документа, а в случае функции `search.ismatch` оценка документа не будет изменена. Следующие перегрузки этой функции доступны с параметрами, идентичными параметрам функции `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

В одном критерии фильтра можно использовать функции `search.ismatch` и `search.ismatchscoring`.

## <a name="examples"></a>Примеры

Найти документы со словом waterfront. Этот запрос фильтрации идентичен [поисковому запросу](https://docs.microsoft.com/rest/api/searchservice/search-documents) с `search=waterfront`:

    search.ismatchscoring('waterfront')

Найти документы со словом hostel и рейтингом, большим или равным 4, или документы со словом motel и рейтингом 5. Обратите внимание, что этот запрос невозможно составить без функции `search.ismatchscoring`.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Найти документы без слова luxury.

    not search.ismatch('luxury')

Найти документы с фразой "ocean view" или рейтингом 5. Запрос `search.ismatchscoring` будет выполняться только по отношению к полям `HotelName` и `Rooms/Description`.

Документы, которые соответствуют только второму предложению дизъюнкции, будут возвращены с `Rating`, равным 5. Чтобы сделать так, чтобы эти документы не соответствовали ни одной из оцененных частей выражения, они будут возвращены с оценками, равными нулю.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Поиск документов, в которых термины «Гостиница» и «аэропорт» находятся в пределах 5 слов друг от друга в описании отеля, а Курение не разрешены по крайней мере в некоторых комнатах. В этом запросе используется [полный язык запросов Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в Когнитивный поиск Azure](search-filters.md)
- [Общие сведения о языке выражений OData для Azure Когнитивный поиск](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для Azure Когнитивный поиск](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Когнитивный поиск Azure REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
