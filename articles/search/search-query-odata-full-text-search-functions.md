---
title: Ссылка на функцию полнотекстовых функций OData
titleSuffix: Azure Cognitive Search
description: Функции полнотекстовых поиска OData, search.ismatch и search.ismatchscoring в запросах Azure Cognitive Search.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793347"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Функции полнотекстовых поиска OData в `search.ismatch` Azure Cognitive Search - и`search.ismatchscoring`

Azure Cognitive Search поддерживает полнотекстовый поиск в контексте `search.ismatch` [выражений фильтра OData](query-odata-filter-orderby-syntax.md) через `search.ismatchscoring` и функции. Эти функции позволяют комбинировать полнотекстовый поиск со строгой фильтрацией Boolean `search` таким образом, что это невозможно только с помощью верхнего уровня параметра [Search API.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

> [!NOTE]
> `search.ismatch` Функции `search.ismatchscoring` и функции поддерживаются только в фильтрах в [API поиска.](https://docs.microsoft.com/rest/api/searchservice/search-documents) Они не поддерживаются в AIS [Suggest](https://docs.microsoft.com/rest/api/searchservice/suggestions) или [Autocomplete.](https://docs.microsoft.com/rest/api/searchservice/autocomplete)

## <a name="syntax"></a>Синтаксис

Следующая форма EBNF[(Расширенная форма Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) `search.ismatchscoring` ) определяет грамматику и функции: `search.ismatch`

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Также доступна интерактивная диаграмма синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Смотрите [ссылку синтаксиса выражения OData для Azure Cognitive Search](search-query-odata-syntax-reference.md) для полного EBNF.

### <a name="searchismatch"></a>search.ismatch

Функция `search.ismatch` оценивает полнотекстовый поисковый запрос как часть выражения фильтра. Документы, соответствующие поисковому запросу, будут возвращены в результирующем наборе. Доступны следующие перегрузки этой функции:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Параметры определены в следующей таблице:

| Имя параметра | Тип | Описание |
| --- | --- | --- |
| `search` | `Edm.String` | Поисковый запрос (в [simple](query-simple-syntax.md) синтаксисе запроса Lucene или [полной lucene).](query-lucene-syntax.md) |
| `searchFields` | `Edm.String` | Запятый разделенный список поисковых полей для поиска в; по умолчанию для всех поисковых полей в индексе. При использовании [полевого поиска](query-lucene-syntax.md#bkmk_fields) в `search` параметре спецификаторы полей в запросе Lucene переопределяют все поля, указанные в этом параметре. |
| `queryType` | `Edm.String` | `'simple'`или; `'full'` по умолчанию . `'simple'` Указывает, какой язык запроса использовался в параметре `search`. |
| `searchMode` | `Edm.String` | `'any'`или `'all'`, по `'any'`умолчанию . Указывает, должны ли быть сопоставлены любые или все условия поиска в `search` параметре, чтобы отсчитать документ как совпадение. При использовании [операторов Lucene Boolean](query-lucene-syntax.md#bkmk_boolean) в параметре `search` они будут иметь приоритет над этим параметром. |

Все вышеперечисленные параметры эквивалентны соответствующим [параметрам поискового запроса в API поиска.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

Функция `search.ismatch` возвращает значение типа `Edm.Boolean`, которое позволяет составить его с другими подвыражениями фильтра с помощью [логических операторов](search-query-odata-logical-operators.md) Boolean.

> [!NOTE]
> Azure Cognitive Search не `search.ismatch` `search.ismatchscoring` поддерживает использование или внутри выражений лямбда. Это означает, что невозможно написать фильтры на коллекции объектов, которые могут соотносить полнотекстовые поисковые совпадения со строгими совпадениями фильтров на одном объекте. Более подробную информацию об этом ограничении, а также о примерах можно найти [в Azure Cognitive Search.](search-query-troubleshoot-collection-filters.md) Более подробную информацию о том, почему существует это ограничение, можно найти [в Azure Cognitive Search.](search-query-understand-collection-filters.md)


### <a name="searchismatchscoring"></a>search.ismatchscoring

Функция, `search.ismatchscoring` как `search.ismatch` и функция, возвращает для `true` документов, которые соответствуют полнотекстовым поисковым запросам, пройденным как параметр. Разница между ними заключается в том, что оценка релевантности документов, соответствующих запросу функции `search.ismatchscoring`, будет влиять на общую оценку документа, а в случае функции `search.ismatch` оценка документа не будет изменена. Следующие перегрузки этой функции доступны с параметрами, идентичными параметрам функции `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Как, `search.ismatch` `search.ismatchscoring` так и функции могут быть использованы в одном и том же выражении фильтра.

## <a name="examples"></a>Примеры

Найти документы со словом waterfront. Этот запрос фильтрации идентичен [поисковому запросу](https://docs.microsoft.com/rest/api/searchservice/search-documents) с `search=waterfront`:

    search.ismatchscoring('waterfront')

Найти документы со словом hostel и рейтингом, большим или равным 4, или документы со словом motel и рейтингом 5. Обратите внимание, что этот запрос невозможно составить без функции `search.ismatchscoring`.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Найти документы без слова luxury.

    not search.ismatch('luxury')

Найти документы с фразой "ocean view" или рейтингом 5. Запрос `search.ismatchscoring` будет выполняться только по отношению к полям `HotelName` и `Rooms/Description`.

Документы, которые соответствовали только второму пункту запрета, `Rating` также будут возвращены - отели с равней 5. Чтобы было ясно, что эти документы не соответствовали ни одной из забитых частей выражения, они будут возвращены со счетом, равным нулю.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Найти документы, где термины "отель" и "аэропорт" находятся в пределах 5 слов друг от друга в описании отеля, и где курение не допускается, по крайней мере, в некоторых номерах. В этом запросе используется [полный язык запросов Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в когнитивном поиске Azure](search-filters.md)
- [Обзор языка выражения OData для когнитивного поиска Azure](query-odata-filter-orderby-syntax.md)
- [Ссылка синтаксиса выражения OData для когнитивного поиска Azure](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Azure Когнитивный поиск REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
