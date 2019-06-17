---
title: Справочник по функциям компонент full-text search OData - поиска Azure
description: Компонент full-text search функции OData, search.ismatch и search.ismatchscoring в запросах поиска Azure.
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
ms.openlocfilehash: 158312a7afe88e7b9885376c5d28b01958acbbfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079812"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>Функции полнотекстового поиска OData в службе поиска Azure — `search.ismatch` и `search.ismatchscoring`

Поиск Azure поддерживает полнотекстовый поиск в контексте [выражения фильтров OData](query-odata-filter-orderby-syntax.md) через `search.ismatch` и `search.ismatchscoring` функции. Эти функции позволяют объединять полнотекстового поиска со строгой логическое фильтрацией способами, которые создаются не только с помощью верхнего уровня `search` параметр [API поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> `search.ismatch` И `search.ismatchscoring` функции поддерживаются только в фильтрах в [API поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents). Они не поддерживаются в [предложить](https://docs.microsoft.com/rest/api/searchservice/suggestions) или [автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete) API-интерфейсы.

## <a name="syntax"></a>Синтаксис

Следующие EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику `search.ismatch` и `search.ismatchscoring` функции:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Также доступна схему интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> См. в разделе [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md) для завершения EBNF.

### <a name="searchismatch"></a>Search.IsMatch

`search.ismatch` Функция вычисляет запрос полнотекстового поиска как часть критерия фильтра. Документы, соответствующие поисковому запросу, будут возвращены в результирующем наборе. Доступны следующие перегрузки этой функции:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Параметры определяются в следующей таблице:

| Имя параметра | type | Описание |
| --- | --- | --- |
| `search` | `Edm.String` | Поисковый запрос (либо [простой](query-simple-syntax.md) или [полный](query-lucene-syntax.md) синтаксис запросов Lucene). |
| `searchFields` | `Edm.String` | Разделенный запятыми список полей для поиска для поиска. по умолчанию для всех полей поиска в индексе. При использовании [были поиска](query-lucene-syntax.md#bkmk_fields) в `search` параметра описателей полей в запросе Lucene переопределить все поля, заданные в этом параметре. |
| `queryType` | `Edm.String` | `'simple'` или `'full'`; по умолчанию используется `'simple'`. Указывает, какой язык запроса использовался в параметре `search`. |
| `searchMode` | `Edm.String` | `'any'` или `'all'`, по умолчанию используется `'any'`. Указывает ли любые или все из поиска терминов в `search` совпадающих для учета документа как сопоставление. При использовании [операторы Lucene Boolean](query-lucene-syntax.md#bkmk_boolean) в `search` параметра, они будет иметь приоритет над этот параметр. |

Все из перечисленных выше параметров эквивалентны в соответствующий [поиска параметров запроса в API поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents).

`search.ismatch` Функция возвращает значение типа `Edm.Boolean`, позволяющий выполнять объединен других вложенных выражений фильтра, с помощью логического [логические операторы](search-query-odata-logical-operators.md).

> [!NOTE]
> Поиск Azure не поддерживает использование `search.ismatch` или `search.ismatchscoring` внутри лямбда-выражения. Это означает, что не можете создавать фильтры по сравнению с коллекциями объектов, которые можно сопоставлять полнотекстового поиска совпадения с строгой фильтрации по тому же объекту. Дополнительные сведения о это ограничение, а также примеры, см. в разделе [Устранение неполадок коллекции фильтров в поиске Azure](search-query-troubleshoot-collection-filters.md). Более подробные сведения о том, почему это ограничение существует, см. в разделе [Общие сведения о фильтрах коллекции в поиске Azure](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Search.ismatchscoring

`search.ismatchscoring` Функция, как и `search.ismatch` возврата функции `true` для документов, соответствующих запросу компонент full-text search, переданный в качестве параметра. Разница между ними заключается в том, что оценка релевантности документов, соответствующих запросу функции `search.ismatchscoring`, будет влиять на общую оценку документа, а в случае функции `search.ismatch` оценка документа не будет изменена. Следующие перегрузки этой функции доступны с параметрами, идентичными параметрам функции `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Как `search.ismatch` и `search.ismatchscoring` функции могут использоваться в одном выражении фильтра.

## <a name="examples"></a>Примеры

Найти документы со словом waterfront. Этот запрос фильтрации идентичен [поисковому запросу](https://docs.microsoft.com/rest/api/searchservice/search-documents) с `search=waterfront`:

    search.ismatchscoring('waterfront')

Найти документы со словом hostel и рейтингом, большим или равным 4, или документы со словом motel и рейтингом 5. Обратите внимание, что этот запрос невозможно составить без функции `search.ismatchscoring`.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Найти документы без слова luxury.

    not search.ismatch('luxury')

Найти документы с фразой "ocean view" или рейтингом 5. Запрос `search.ismatchscoring` будет выполняться только по отношению к полям `HotelName` и `Rooms/Description`.

Документы, которые соответствуют только второе предложение логического сложения возвращается слишком--гостиницы с `Rating` равно 5. Чтобы сделать его очистки, что эти документы не соответствует ни одному из оцененных части выражения, они будут возвращены с оценкой, равным нулю.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Поиск документов, где условия «отель» и «airport» находятся в пределах 5 слов друг от друга в описании гостиницы, и где курящих не допускается в по крайней мере некоторые из комнаты. В этом запросе используется [полный язык запросов Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Общие сведения о языках выражений OData для службы поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
