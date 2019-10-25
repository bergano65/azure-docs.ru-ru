---
title: Справочник по функциям поиска OData. Score
titleSuffix: Azure Cognitive Search
description: Функция поиска OData. Score в запросах Когнитивный поиск Azure.
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
ms.openlocfilehash: 500ac4f3a44d54e367ddc4ee5efc9514d603cab6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793272"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Функция `search.score` OData в Azure Когнитивный поиск

При отправке запроса в Когнитивный поиск Azure без [параметра **$OrderBy** ](search-query-odata-orderby.md)возвращенные результаты будут отсортированы в порядке убывания по релевантности. Даже если вы используете **$OrderBy**, показатель релевантности будет использоваться для разрыва связей по умолчанию. Однако иногда полезно использовать показатель релевантности в качестве начального критерия сортировки, а некоторые другие критерии — как средство разбиения. Функция `search.score` позволяет сделать это.

## <a name="syntax"></a>Синтаксис

Синтаксис для `search.score` в **$orderby** — `search.score()`. Функция `search.score` не принимает параметров. Его можно использовать с описателем порядка сортировки `asc` или `desc`, как и любое другое предложение в параметре **$OrderBy** . Он может находиться в любом месте в списке критериев сортировки.

## <a name="example"></a>Пример

Сортировка гостиниц в убывающем порядке по `search.score` и `rating`, а затем в порядке возрастания по расстоянию от заданных координат, чтобы между двумя гостиницами с одинаковыми рейтингами первым был указан ближайший друг от друга:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Дальнейшие действия  

- [Общие сведения о языке выражений OData для Azure Когнитивный поиск](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для Azure Когнитивный поиск](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;API Azure когнитивный Поиск EST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
