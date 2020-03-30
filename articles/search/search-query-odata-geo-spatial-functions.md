---
title: Ссылка на геопространственную функцию OData
titleSuffix: Azure Cognitive Search
description: Синтаксис и справочная документация для использования геопространственных функций OData, geo.distance и geo.intersects в запросах Azure Cognitive Search.
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113176"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Геопространственные функции OData в Azure `geo.distance` Cognitive Search - и`geo.intersects`

Azure Cognitive Search поддерживает геопространственные запросы в [выражениях фильтра OData](query-odata-filter-orderby-syntax.md) через `geo.distance` `geo.intersects` и функции. Функция `geo.distance` возвращает расстояние в километрах между двумя точками, одна из которых является полем или переменной диапазона, а одна — постоянной, пройденой как часть фильтра. Функция `geo.intersects` возвращается, `true` если заданная точка находится в пределах заданного полигона, где точка является переменной поля или диапазона, а полигон указан как константа, пройденного как часть фильтра.

Функция `geo.distance` также может быть использована в [ **$orderby** параметрдля](search-query-odata-orderby.md) для сортировки результатов поиска на расстоянии от данной точки. Синтаксис для `geo.distance` в **$orderby** такой же, как и в **$filter**. При `geo.distance` использовании в **$orderby,** поле, к которому оно применяется, должно быть типа, `Edm.GeographyPoint` и оно также должно быть **сортируемым.**

> [!NOTE]
> При `geo.distance` использовании в **$orderby** параметре поле, перейдите к функции, должно содержать только одну геоточку. Другими словами, он `Edm.GeographyPoint` должен `Collection(Edm.GeographyPoint)`быть типа, а не . Невозможно сортировать поля сбора в Azure Cognitive Search.

## <a name="syntax"></a>Синтаксис

Следующая форма EBNF[(Расширенная форма Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику `geo.distance` и `geo.intersects` функции, а также геопространственные значения, на которых они работают:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Также доступна интерактивная диаграмма синтаксиса:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для когнитивного поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Смотрите [ссылку синтаксиса выражения OData для Azure Cognitive Search](search-query-odata-syntax-reference.md) для полного EBNF.

### <a name="geodistance"></a>geo.distance

Функция `geo.distance` принимает два параметра `Edm.GeographyPoint` типа `Edm.Double` и возвращает значение, которое расстояние между ними в километрах. Это отличается от других служб, поддерживающих геопространственные операции OData, которые обычно возвращают расстояния в метрах.

Одним из параметров `geo.distance` должен быть константа точки географии, а другой должен быть полевым путем (или `Collection(Edm.GeographyPoint)`переменным диапазона в случае итерации фильтра над полем типа). Порядок этих параметров не имеет значения.

География точка константа является формой, `geography'POINT(<longitude> <latitude>)'`где долгота и широта численных констант.

> [!NOTE]
> При `geo.distance` использовании в фильтре, вы должны сравнить расстояние, `lt` `le`возвращенное функцией с постоянным использованием, `gt`или. `ge` Операторы `eq` и `ne` не предназначены для сравнения расстояний. Например, это правильное `geo.distance`использование `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`: .

### <a name="geointersects"></a>geo.intersects

Функция `geo.intersects` принимает переменную `Edm.GeographyPoint` типа и `Edm.GeographyPolygon` постоянную `Edm.Boolean`  --  `true` и возвращает, если точка находится в `false` пределах полигона, в противном случае.

Полигон представляет собой двухмерную поверхность, хранящуюся в виде последовательности точек, определяющих ограничивающее кольцо [(см. приведенные ниже примеры).](#examples) Многоугольник должен быть замкнут, то есть первая и конечная точки должны совпадать. [Точки многоугольника должны наноситься в порядке против часовой стрелки](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Геопространственные запросы и полигоны, охватывающие 180-й меридиан

Для многих библиотек геопространственных запросов, формулирующие запрос, включающий 180-й меридиан (около линии даты), либо закрыты, либо требуют обходного пути, например, разделив полигон на два, по одному по обе стороны от меридиана.

В Azure Cognitive Search геопространственные запросы, включающие 180-градусную долготу, будут работать, как ожидалось, если форма запроса прямоугольна, а `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`ваши координаты совпадают с расположением сетки вдоль долготы и широты (например, ). В противном случае для непрямоугольных или невыровненных фигур используйте подход с разделенным многоугольником.  

### <a name="geo-spatial-functions-and-null"></a>Геопространственные функции и`null`

Как и все другие поля, не собираемые в Azure Cognitive Search, поля типа `Edm.GeographyPoint` могут содержать `null` значения. Когда Azure Cognitive `geo.intersects` Search оценивает поле, которое есть, `null`результат всегда будет `false`. Поведение `geo.distance` в данном случае зависит от контекста:

- В фильтрах, `geo.distance` `null` поля приводит `null`к . Это означает, что документ `null` не будет соответствовать, потому что по сравнению с любой ненулевой стоимости оценивается `false`в .
- При сортировке `geo.distance` результаты `null` с помощью **$orderby,** поля приводит к максимально возможному расстоянию. Документы с таким полем будут сортировать `asc` ниже, чем все другие, когда используется `desc`направление сортировки (по умолчанию), и выше, чем все другие, когда направление .

## <a name="examples"></a>Примеры

### <a name="filter-examples"></a>Примеры выражений фильтрации

Найти все отели в пределах 10 километров от данной `Edm.GeographyPoint`точки отсчета (где расположение является полетипа типа):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Найти все отели в пределах данного viewport описывается как `Edm.GeographyPoint`полигон (где расположение области типа). Обратите внимание, что многоугольник замкнут (первая и последняя точки должны совпадать, и [точки должны быть нанесены в порядке против часовой стрелки](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Примеры выражений упорядочивания

Сортировать отели `rating`спускаясь, то восхождение на расстояние от данных координат:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Сортировать отели в `search.score` `rating`порядке убывания и, а затем в порядке возрастания на расстоянии от данных координат так, что между двумя отелями с одинаковыми рейтингами, ближайший из них указан первым:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в когнитивном поиске Azure](search-filters.md)
- [Обзор языка выражения OData для когнитивного поиска Azure](query-odata-filter-orderby-syntax.md)
- [Ссылка синтаксиса выражения OData для когнитивного поиска Azure](search-query-odata-syntax-reference.md)
- [Поиск документов &#40;Azure Когнитивный поиск REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
