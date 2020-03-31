---
title: Ссылка на функцию OData search.score
titleSuffix: Azure Cognitive Search
description: Синтаксис и справочная документация для использования функции search.score в запросах Azure Cognitive Search.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113141"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Функция `search.score` OData в когнитивном поиске Azure

При отправке запроса в Azure Cognitive Search без [ **$orderby** параметра](search-query-odata-orderby.md)результаты, которые возвращаются, будут отсортированы в порядке убывания по оценке релевантности. Даже когда вы используете **$orderby,** оценка релевантности будет использоваться для разрыва связей по умолчанию. Однако иногда полезно использовать оценку релевантности в качестве исходного критерия сортировки и некоторых других критериев в качестве тай-брейка. Функция `search.score` позволяет это сделать.

## <a name="syntax"></a>Синтаксис

Синтаксис для `search.score` в **$orderby** — `search.score()`. Функция `search.score` не принимает параметров. Он может быть `asc` использован `desc` с или сортировочнитель или сортов, как и любой другой пункт в **$orderby** параметре. Он может появиться в любом месте списка критериев сортировки.

## <a name="example"></a>Пример

Сортировать отели в `search.score` `rating`порядке убывания и, а затем в порядке возрастания на расстоянии от данных координат так, что между двумя отелями с одинаковыми рейтингами, ближайший из них указан первым:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Дальнейшие действия  

- [Обзор языка выражения OData для когнитивного поиска Azure](query-odata-filter-orderby-syntax.md)
- [Ссылка синтаксиса выражения OData для когнитивного поиска Azure](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Azure когнитивного поиска EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
