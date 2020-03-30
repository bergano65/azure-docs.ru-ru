---
title: Ссылка на заказ OData
titleSuffix: Azure Cognitive Search
description: Синтаксис и языковая справочная документация для использования заказов в запросах Azure Cognitive Search.
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113147"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData $orderby синтаксис в когнитивном поиске Azure

 Вы можете использовать [параметр OData **$orderby** ](query-odata-filter-orderby-syntax.md) для применения пользовательского заказа сортировки для результатов поиска в Azure Cognitive Search. В этой статье подробно описывается синтаксис **$orderby.** Более подробную информацию о том, как использовать **$orderby** при представлении результатов поиска, можно [найти в Azure Cognitive Search.](search-pagination-page-layout.md)

## <a name="syntax"></a>Синтаксис

Параметр **$orderby** принимает разделенный на запятую список до 32 **положений заказа.** Синтаксис оговорки по заказу описан следующим eBNF[(Расширенная форма Backus-Naur):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Также доступна интерактивная диаграмма синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Смотрите [ссылку синтаксиса выражения OData для Azure Cognitive Search](search-query-odata-syntax-reference.md) для полного EBNF.

Каждое положение имеет критерии сортировки, по желанию следуют сортировки`asc` (для восходящего или `desc` для спуска). Если вы не укажете направление, значение значения по умолчанию возрастает. Критериями сортировки могут `sortable` быть либо путь поля, либо вызов к функциям [`geo.distance`](search-query-odata-geo-spatial-functions.md) или функциям. [`search.score`](search-query-odata-search-score-function.md)

Если несколько документов имеют `search.score` одинаковые критерии сортировки и функция не `Rating` используется (например, если вы сортируете по числовому полю и три документа имеют рейтинг 4), связи будут нарушены счетом документа в порядке убывания. Когда оценки документов одинаковы (например, когда в запросе нет полнотекстовых поисковых запросов), относительный заказ связанных документов неопределенный.

Вы можете определить несколько условий сортировки. Порядок выражений определяет окончательный порядок сортировки. Например, для сортировки нисходящего балла, за `$orderby=search.score() desc,Rating desc`которым следует рейтинг, синтаксис будет .

Синтаксис для `geo.distance` в **$orderby** такой же, как и в **$filter**. При использовании функции `geo.distance` в **$orderby** поле, к которому она применяется, должно быть сортируемым (`sortable`) и иметь тип `Edm.GeographyPoint`.

Синтаксис для `search.score` в **$orderby** — `search.score()`. Функция `search.score` не принимает никаких параметров.

## <a name="examples"></a>Примеры

Сортировать отели по возрастанию базового тарифа:

    $orderby=BaseRate asc

Сортировать отели по убыванию рейтинга, а затем по возрастанию базового тарифа (помните, что по умолчанию сортировка происходит по возрастанию):

    $orderby=Rating desc,BaseRate

Сортировать отели, спускающиеся по рейтингу, затем поднимаясь на расстояние от данных координат:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Сортировать отели в порядке убывания по search.score и рейтингу, а затем в порядке возрастания на расстоянии от данных координат. Между двумя отелями с одинаковыми показателями релевантности и рейтингами, самый близкий указан первым:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Дальнейшие действия  

- [Как работать с результатами поиска в Azure Cognitive Search](search-pagination-page-layout.md)
- [Обзор языка выражения OData для когнитивного поиска Azure](query-odata-filter-orderby-syntax.md)
- [Ссылка синтаксиса выражения OData для когнитивного поиска Azure](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Azure Когнитивный поиск REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
