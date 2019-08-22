---
title: Порядок сортировки по Справочнику OData — Поиск Azure
description: Справочник по языку OData для синтаксиса ORDER-BY в запросах поиска Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 8ee44549931100a1affa5e2bb9e5cda904c05ed1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647539"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Синтаксис $orderby OData в поиске Azure

 [Параметр **$OrderBy** OData](query-odata-filter-orderby-syntax.md) можно использовать для применения пользовательского порядка сортировки результатов поиска в службе поиска Azure. В этой статье подробно описывается синтаксис **$OrderBy** . Дополнительные общие сведения об использовании **$OrderBy** при представлении результатов поиска см. [в статье как работать с результатами поиска в службе поиска Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Синтаксис

Параметр **$OrderBy** принимает разделенный запятыми список из 32 **предложений упорядочения**. Синтаксис предложения ORDER-BY описывается следующим EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Доступна также интерактивная схема синтаксиса:

> [!div class="nextstepaction"]
> [Схема синтаксиса OData для поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md) .

Каждое предложение имеет критерий сортировки, при необходимости за которым следует направление сортировки`asc` (для по возрастанию или `desc` по убыванию). Если не указать направление, по умолчанию используется значение по возрастанию. Критерий сортировки может быть либо путем `sortable` к полю, либо вызовом либо [`search.score`](search-query-odata-search-score-function.md) функций, [`geo.distance`](search-query-odata-geo-spatial-functions.md) либо.

Если несколько документов имеют одинаковые условия сортировки и `search.score` функция не используется (например, если сортировка выполняется по числовому `Rating` полю, а все три документа имеют оценку 4), то в убывающем порядке они будут разорваны. Если оценки документа одинаковы (например, если в запросе не указан запрос полнотекстового поиска), относительный порядок связанных документов является неопределенным.

Вы можете определить несколько условий сортировки. Порядок выражений определяет окончательный порядок сортировки. Например, для сортировки по убыванию по показателям, за которыми следует оценка, синтаксис будет выглядеть `$orderby=search.score() desc,Rating desc`так:.

Синтаксис для `geo.distance` в **$orderby** такой же, как и в **$filter**. При использовании функции `geo.distance` в **$orderby** поле, к которому она применяется, должно быть сортируемым (`sortable`) и иметь тип `Edm.GeographyPoint`.

Синтаксис для `search.score` в **$orderby** — `search.score()`. Функция `search.score` не принимает никаких параметров.

## <a name="examples"></a>Примеры

Сортировать отели по возрастанию базового тарифа:

    $orderby=BaseRate asc

Сортировать отели по убыванию рейтинга, а затем по возрастанию базового тарифа (помните, что по умолчанию сортировка происходит по возрастанию):

    $orderby=Rating desc,BaseRate

Сортировка гостиниц по убыванию по рейтингу, затем по возрастанию по расстоянию от заданных координат:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Сортировка гостиниц в порядке убывания по поиску. Оценка и оценка, а затем в порядке возрастания по расстоянию от заданных координат. Между двумя гостиницами с одинаковыми показателями релевантности и рейтингами в первую очередь указывается ближайшее значение:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Следующие шаги  

- [Как работать с результатами поиска в службе "Поиск Azure"](search-pagination-page-layout.md)
- [Общие сведения о языке выражений OData для поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
