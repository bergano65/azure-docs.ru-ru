---
title: Справочник по функциям поиска OData. Score
titleSuffix: Azure Cognitive Search
description: Справочная документация по синтаксису и документации по использованию функции поиска. Score в Когнитивный поиск запросов Azure.
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
ms.openlocfilehash: c31304228d9629b0df7f7511ecca2616b4891ee7
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206960"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>`search.score`Функция OData в Azure когнитивный Поиск

При отправке запроса в Когнитивный поиск Azure без [параметра **$OrderBy** ](search-query-odata-orderby.md)возвращенные результаты будут отсортированы в порядке убывания по релевантности. Даже если вы используете **$OrderBy**, показатель релевантности будет использоваться для разрыва связей по умолчанию. Однако иногда полезно использовать показатель релевантности в качестве начального критерия сортировки, а некоторые другие критерии — как средство разбиения. `search.score`Эта функция позволяет сделать это.

## <a name="syntax"></a>Синтаксис

Синтаксис для `search.score` в **$orderby** — `search.score()`. Функция `search.score` не принимает параметров. Его можно использовать с `asc` описателем или с помощью `desc` спецификатора сортировки, как и любое другое предложение в параметре **$OrderBy** . Он может находиться в любом месте в списке критериев сортировки.

## <a name="example"></a>Пример

Сортировать Гостиницы в убывающем порядке по `search.score` и `rating` , а затем в возрастающем порядке по расстоянию от заданных координат, чтобы между двумя гостиницами с одинаковыми рейтингами первым был указан ближайший вариант:

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Дальнейшие действия  

- [Общие сведения о языке выражений OData для Azure Когнитивный поиск](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для Azure Когнитивный поиск](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;API Azure Когнитивный поиск EST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
