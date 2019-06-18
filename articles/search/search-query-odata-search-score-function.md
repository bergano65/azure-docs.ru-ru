---
title: Справочник по функциям search.score OData - поиска Azure
description: Функция search.score OData в запросах поиска Azure.
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
ms.openlocfilehash: dc444216c4677b9970b867e92aa5ae259a197220
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079695"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` функция в службе поиска Azure

При отправке запроса в службу поиска Azure без [ **$orderby** параметр](search-query-odata-orderby.md), полученных результатов будут отсортированы в убывающем порядке по оценке релевантности. Даже при использовании **$orderby**, Оценка релевантности, которая будет использоваться для разорвать связи по умолчанию. Тем не менее иногда полезно служит решающим Оценка релевантности как начальная сортировки и некоторых других критериев. `search.score` Функция позволяет это сделать.

## <a name="syntax"></a>Синтаксис

Синтаксис для `search.score` в **$orderby** — `search.score()`. Функция `search.score` не принимает параметров. Он может использоваться с `asc` или `desc` описатель порядок сортировки, так же, как другие предложения в **$orderby** параметра. Он может находиться в любом месте список критериев сортировки.

## <a name="example"></a>Пример

Сортировать гостиницы, в убывающем порядке по `search.score` и `rating`, а затем в порядке возрастания значений расстояние от точки, заданной координатами таким образом, чтобы между двумя гостиницы с идентичными оценки ближайшая указывается в первую очередь:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Дальнейшие действия  

- [Общие сведения о языках выражений OData для службы поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
