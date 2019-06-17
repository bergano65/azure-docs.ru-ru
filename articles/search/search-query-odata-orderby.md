---
title: Предложение order by Справочник по протоколу OData - поиска Azure
description: Справочник по языку OData для синтаксиса предложение order by в запросах поиска Azure.
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
ms.openlocfilehash: 1ced35dc73e6d596fbeda32590ab0b69df396c5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079760"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Синтаксис OData $orderby в службе поиска Azure

 Можно использовать [OData **$orderby** параметр](query-odata-filter-orderby-syntax.md) применить пользовательский порядок сортировки для результатов поиска в службе поиска Azure. В этой статье описывается синтаксис **$orderby** подробно. Более общие сведения об использовании **$orderby** при представлении результатов поиска, см. в разделе [способы работы со службой поиска результатов в службе поиска Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Синтаксис

**$Orderby** параметр принимает разделенный запятыми список до 32 **предложений order by**. Синтаксис предложения order by, описываемого ниже EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Также доступна схему интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> См. в разделе [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md) для завершения EBNF.

Каждое предложение имеет критерии сортировки, при желании указав направление сортировки (`asc` для по возрастанию или `desc` по убыванию). Если не указать направление, по умолчанию — по возрастанию. Критерии сортировки может быть путь к `sortable` поля или вызова [ `geo.distance` ](search-query-odata-geo-spatial-functions.md) или [ `search.score` ](search-query-odata-search-score-function.md) функции.

Если несколько документов имеют одинаковые условия сортировки и `search.score` функция не используется (например, в том случае, если сортировка с числами `Rating` поля и три документа все имеющие рейтинг 4), равенстве по оценке документа в порядке убывания. Если оценки документа (например, когда запросы полнотекстового поиска, не указанный в запросе), затем относительный порядок равноценных документы не определен.

Вы можете определить несколько условий сортировки. Порядок выражений определяет окончательный порядок сортировки. Например, для сортировки по убыванию по оценке, следуют оценку, синтаксис будет `$orderby=search.score() desc,Rating desc`.

Синтаксис для `geo.distance` в **$orderby** такой же, как и в **$filter**. При использовании функции `geo.distance` в **$orderby** поле, к которому она применяется, должно быть сортируемым (`sortable`) и иметь тип `Edm.GeographyPoint`.

Синтаксис для `search.score` в **$orderby** — `search.score()`. Функция `search.score` не принимает никаких параметров.

## <a name="examples"></a>Примеры

Сортировать отели по возрастанию базового тарифа:

    $orderby=BaseRate asc

Сортировать отели по убыванию рейтинга, а затем по возрастанию базового тарифа (помните, что по умолчанию сортировка происходит по возрастанию):

    $orderby=Rating desc,BaseRate

Сортировать по убыванию, оценка, а затем по возрастанию, расстояние от точки, заданной координатами гостиницы:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Сортировать гостиницы, в убывающем порядке по search.score и оценки, а затем в возрастающем порядке по расстояние от точки, заданной координатами. Между двумя гостиницы с идентичными релевантности и оценок ближайшая указывается в первую очередь:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Дальнейшие действия  

- [Как работать со службой поиска результатов в службе поиска Azure](search-pagination-page-layout.md)
- [Общие сведения о языках выражений OData для службы поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
