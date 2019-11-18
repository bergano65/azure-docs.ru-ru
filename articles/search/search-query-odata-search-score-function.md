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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113141"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Функция `search.score` OData в Azure Когнитивный поиск

При отправке запроса в Когнитивный поиск Azure без [параметра **$OrderBy** ](search-query-odata-orderby.md)возвращенные результаты будут отсортированы в порядке убывания по релевантности. Даже если вы используете **$OrderBy**, показатель релевантности будет использоваться для разрыва связей по умолчанию. Однако иногда полезно использовать показатель релевантности в качестве начального критерия сортировки, а некоторые другие критерии — как средство разбиения. Функция `search.score` позволяет сделать это.

## <a name="syntax"></a>Синтаксис

Синтаксис для `search.score` в **$orderby** — `search.score()`. Функция `search.score` не принимает параметров. Его можно использовать с описателем порядка сортировки `asc` или `desc`, как и любое другое предложение в параметре **$OrderBy** . Он может находиться в любом месте в списке критериев сортировки.

## <a name="example"></a>Пример

Сортировка гостиниц в убывающем порядке по `search.score` и `rating`, а затем в порядке возрастания по расстоянию от заданных координат, чтобы между двумя гостиницами с одинаковыми рейтингами первым был указан ближайший друг от друга:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Дополнительная информация  

- [Общие сведения о языке выражений OData для Azure Когнитивный поиск](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для Azure Когнитивный поиск](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;API Azure когнитивный Поиск EST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
