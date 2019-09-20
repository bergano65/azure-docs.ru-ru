---
title: Справочник по функциям полнотекстового поиска OData — Поиск Azure
description: Функции полнотекстового поиска OData, Search. gettext и Search. исматчскоринг в запросах поиска Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: c3b28c8799b09ddfe008df8539709c5a704ac6b4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648015"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>Функции полнотекстового поиска OData в службе поиска Azure — `search.ismatch` и`search.ismatchscoring`

Поиск Azure поддерживает полнотекстовый поиск в контексте [выражений фильтра OData](query-odata-filter-orderby-syntax.md) с помощью `search.ismatch` функций и `search.ismatchscoring` . Эти функции позволяют объединять полнотекстовый поиск с помощью исключительной логической фильтрации, так как это невозможно только с помощью параметра верхнего уровня `search` [API поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> Функции `search.ismatch` и`search.ismatchscoring` поддерживаются только в фильтрах в [API поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents). Они не поддерживаются в интерфейсах API [предложения](https://docs.microsoft.com/rest/api/searchservice/suggestions) или [автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Синтаксис

Следующая EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику `search.ismatch` функций и `search.ismatchscoring` .

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
> [Схема синтаксиса OData для поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md) .

### <a name="searchismatch"></a>Поиск. Match

`search.ismatch` Функция вычисляет полнотекстовый поисковый запрос как часть критерия фильтра. Документы, соответствующие поисковому запросу, будут возвращены в результирующем наборе. Доступны следующие перегрузки этой функции:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Параметры определены в следующей таблице.

| Имя параметра | Тип | Описание |
| --- | --- | --- |
| `search` | `Edm.String` | Поисковый запрос (как [простой](query-simple-syntax.md) , так и [полный](query-lucene-syntax.md) синтаксис запроса Lucene). |
| `searchFields` | `Edm.String` | Разделенный запятыми список полей с возможностью поиска, в которых выполняется поиск; по умолчанию для всех полей, поддерживающих поиск, в индексе. При использовании [поискового](query-lucene-syntax.md#bkmk_fields) запроса в `search` параметре описатели полей в запросе Lucene переопределяют все поля, указанные в этом параметре. |
| `queryType` | `Edm.String` | `'simple'`значение по умолчанию `'simple'`—. `'full'` Указывает, какой язык запроса использовался в параметре `search`. |
| `searchMode` | `Edm.String` | `'any'`значение по умолчанию `'any'`—. `'all'` Указывает, должны ли быть сопоставлены все или все `search` условия поиска в параметре, чтобы подсчитать документ как совпадение. При использовании [логических операторов Lucene](query-lucene-syntax.md#bkmk_boolean) в `search` параметре они будут иметь приоритет над этим параметром. |

Все указанные выше параметры эквивалентны соответствующим [параметрам поискового запроса в API поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Функция `search.ismatch` возвращает значение типа `Edm.Boolean`, которое позволяет составить его с другими подвыражениями фильтра с помощью [логических операторов](search-query-odata-logical-operators.md) Boolean.

> [!NOTE]
> Поиск Azure не поддерживает использование `search.ismatch` лямбда-выражений или `search.ismatchscoring` внутри него. Это означает, что невозможно записать фильтры для коллекций объектов, которые могут сопоставлять полнотекстовые поисковые совпадения с ограниченным совпадением фильтра для одного и того же объекта. Дополнительные сведения об этом ограничении, а также примеры см. [в разделе Устранение неполадок фильтров сбора в службе поиска Azure](search-query-troubleshoot-collection-filters.md). Более подробные сведения о том, почему это ограничение существует, см. в разделе [Основные сведения о фильтрах коллекции в службе поиска Azure](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Поиск. исматчскоринг

Функция, как и функция, возвращает `true` для документов, которые соответствуют запросу полнотекстового поиска, переданному в качестве параметра. `search.ismatch` `search.ismatchscoring` Разница между ними заключается в том, что оценка релевантности документов, соответствующих запросу функции `search.ismatchscoring`, будет влиять на общую оценку документа, а в случае функции `search.ismatch` оценка документа не будет изменена. Следующие перегрузки этой функции доступны с параметрами, идентичными параметрам функции `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

`search.ismatch` Функции и `search.ismatchscoring` могут использоваться в одном и том же критерии фильтра.

## <a name="examples"></a>Примеры

Найти документы со словом waterfront. Этот запрос фильтрации идентичен [поисковому запросу](https://docs.microsoft.com/rest/api/searchservice/search-documents) с `search=waterfront`:

    search.ismatchscoring('waterfront')

Найти документы со словом hostel и рейтингом, большим или равным 4, или документы со словом motel и рейтингом 5. Обратите внимание, что этот запрос невозможно составить без функции `search.ismatchscoring`.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Найти документы без слова luxury.

    not search.ismatch('luxury')

Найти документы с фразой "ocean view" или рейтингом 5. Запрос `search.ismatchscoring` будет выполняться только по отношению к полям `HotelName` и `Rooms/Description`.

Документы, которые совпали только с вторым предложением дизъюнкции, будут возвращены — Гостиницы со `Rating` значением, равным 5. Чтобы сделать так, чтобы эти документы не соответствовали ни одной из оцененных частей выражения, они будут возвращены с оценками, равными нулю.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Поиск документов, в которых термины «Гостиница» и «аэропорт» находятся в пределах 5 слов друг от друга в описании отеля, а Курение не разрешены по крайней мере в некоторых комнатах. В этом запросе используется [полный язык запросов Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Следующие шаги  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Общие сведения о языке выражений OData для поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
