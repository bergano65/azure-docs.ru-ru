---
title: Справочник по геопространственной функции OData — Поиск Azure
description: Геопространственные функции OData, Geo. Distance и Geo. INTERSECT в запросах поиска Azure.
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
ms.openlocfilehash: 9585a9a7ea976ed32ccb8eed1e69877339196f87
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647563"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>Геопространственные функции OData в службе поиска Azure — `geo.distance` и`geo.intersects`

Поиск Azure поддерживает геопространственные запросы в [выражениях фильтра OData](query-odata-filter-orderby-syntax.md) с `geo.distance` помощью функций `geo.intersects` и. `geo.distance` Функция возвращает расстояние в километрах между двумя точками, одно из которых является переменной поля или диапазона, а второй — константой, передаваемой в качестве части фильтра. `geo.intersects` Функция возвращает`true` , если заданная точка находится внутри заданного многоугольника, где точка — это поле или переменная диапазона, а многоугольник — как константа, передаваемая как часть фильтра.

Функция также может использоваться в параметре [ **$OrderBy** ](search-query-odata-orderby.md) для сортировки результатов поиска по расстоянию от заданной точки. `geo.distance` Синтаксис для `geo.distance` в **$orderby** такой же, как и в **$filter**. При использовании `geo.distance` в **$OrderBy**поле, к которому оно применяется, должно иметь тип `Edm.GeographyPoint` , а также быть **сортируемый**.

## <a name="syntax"></a>Синтаксис

Следующая EBNF ([Расширенная форма Backus-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику `geo.distance` функций и `geo.intersects` , а также геопространственных значений, в которых они работают:

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

Доступна также интерактивная схема синтаксиса:

> [!div class="nextstepaction"]
> [Схема синтаксиса OData для поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Полный EBNF см. в [справочнике по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md) .

### <a name="geodistance"></a>географическое расстояние

Функция принимает два параметра типа `Edm.GeographyPoint` и возвращает `Edm.Double` значение, которое является расстоянием между ними в километрах. `geo.distance` Это отличается от других служб, поддерживающих геопространственные операции OData, которые обычно возвращают расстояния в метрах.

Один из параметров `geo.distance` должен быть константой географического объекта, а другой — путем к полю (или переменной диапазона в случае итерации по полю типа `Collection(Edm.GeographyPoint)`). Порядок этих параметров не имеет значения.

Константа географической точки имеет форму `geography'POINT(<longitude> <latitude>)'`, где Долгота и Широта являются числовыми константами.

> [!NOTE]
> При использовании `geo.distance` в фильтре необходимо сравнить расстояние, возвращаемое функцией, с константой, используя `lt`, `le`, `gt`или `ge`. Операторы `eq` и `ne` не предназначены для сравнения расстояний. Например, это правильное использование `geo.distance`:. `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`

### <a name="geointersects"></a>географические пересечения

`Edm.Boolean` `Edm.GeographyPolygon`  --  `false` Функция принимает переменную типа `Edm.GeographyPoint` и константу и возвращает `true` , если точка находится внутри границ многоугольника, в противном случае — значение. `geo.intersects`

Многоугольник — это двухмерная поверхность, которая хранится в виде последовательности точек, определяющих ограничивающее кольцо (см. [примеры](#examples) ниже). Многоугольник должен быть замкнут, то есть первая и конечная точки должны совпадать. [Точки многоугольника должны наноситься в порядке против часовой стрелки](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Геопространственные запросы и многоугольники, охватывающие 180TH меридианов

Для многих библиотек геопространственных запросов, составления запрос, включающий 180TH меридианов (вблизи дателине), не ограничивается или требует обходного пути, например разделение многоугольника на два, один с обеих сторон в меридианов.

В службе поиска Azure геопространственные запросы, включающие долготу в 180 градусов, будут работать ожидаемым образом, если фигура запроса прямоугольная, а координаты совпадают с макетом сетки вдоль долготы и широты (например, `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). В противном случае для непрямоугольных или невыровненных фигур используйте подход с разделенным многоугольником.  

### <a name="geo-spatial-functions-and-null"></a>Геопространственные функции и`null`

Как и все остальные поля, не относящиеся к коллекции, в поиске Azure `Edm.GeographyPoint` поля типа `null` могут содержать значения. Когда поиск Azure выполняет `geo.intersects` `null`оценку поля, то результатом всегда `false`будет. Поведение `geo.distance` в этом случае зависит от контекста:

- В фильтрах `geo.distance` поле приводит `null` `null`к. Это означает, что документ не будет совпадать `null` , так как по сравнению с любым значением, `false`отличным от NULL, вычисляется значение.
- При сортировке результатовс помощью `geo.distance` $OrderBy `null` поле получает максимально возможное расстояние. Документы с таким полем будут сортироваться ниже остальных, если используется направление `asc` сортировки (по умолчанию) и выше, чем все остальные, если направление имеет `desc`значение.

## <a name="examples"></a>Примеры

### <a name="filter-examples"></a>Примеры выражений фильтрации

Найти все гостиницы в 10 километрах заданной контрольной точки (где Location является полем типа `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Найти все гостиницы в заданном окне просмотра, описанные в виде многоугольника (где Location — `Edm.GeographyPoint`поле типа). Обратите внимание, что многоугольник замкнут (первая и последняя точки должны совпадать, и [точки должны быть нанесены в порядке против часовой стрелки](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Примеры выражений упорядочивания

Сортировка гостиниц по `rating`убыванию, затем по возрастанию по расстоянию от заданных координат:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Сортировать Гостиницы в убывающем порядке по `search.score` и `rating`, а затем в возрастающем порядке по расстоянию от заданных координат, чтобы между двумя гостиницами с одинаковыми рейтингами первым был указан ближайший вариант:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Следующие шаги  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Общие сведения о языке выражений OData для поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
