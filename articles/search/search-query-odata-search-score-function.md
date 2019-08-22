---
title: Справочник по функциям поиска OData. Оценка Azure
description: Функция поиска OData. Score в запросах поиска Azure.
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647517"
---
# <a name="odata-searchscore-function-in-azure-search"></a>Функция `search.score` OData в службе поиска Azure

При отправке запроса в службу поиска Azure без параметра [ **$OrderBy** ](search-query-odata-orderby.md)возвращенные результаты будут отсортированы в порядке убывания по релевантности. Даже если вы используете **$OrderBy**, показатель релевантности будет использоваться для разрыва связей по умолчанию. Однако иногда полезно использовать показатель релевантности в качестве начального критерия сортировки, а некоторые другие критерии — как средство разбиения. Эта `search.score` функция позволяет сделать это.

## <a name="syntax"></a>Синтаксис

Синтаксис для `search.score` в **$orderby** — `search.score()`. Функция `search.score` не принимает параметров. Его можно использовать с описателем `asc` или `desc` с помощью спецификатора сортировки, как и любое другое предложение в параметре **$OrderBy** . Он может находиться в любом месте в списке критериев сортировки.

## <a name="example"></a>Пример

Сортировать Гостиницы в убывающем порядке по `search.score` и `rating`, а затем в возрастающем порядке по расстоянию от заданных координат, чтобы между двумя гостиницами с одинаковыми рейтингами первым был указан ближайший вариант:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Следующие шаги  

- [Общие сведения о языке выражений OData для поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
