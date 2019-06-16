---
title: Ссылка геопространственные функции OData - поиска Azure
description: OData геопространственные функции, geo.distance и geo.intersects в запросах поиска Azure.
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
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079799"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>Геопространственные функции OData в службе поиска Azure — `geo.distance` и `geo.intersects`

Поиск Azure поддерживает геопространственные запросы в [выражения фильтров OData](query-odata-filter-orderby-syntax.md) через `geo.distance` и `geo.intersects` функции. `geo.distance` Функция возвращает расстояние в километрах между двумя точками, одна из которых поля или переменная диапазона, а один — константа передаются как часть фильтра. `geo.intersects` Возвращает `true` Если заданная точка находится в пределах указанного многоугольника, где точка — это поля или диапазона, переменная, а многоугольник задается как константа, переданная как часть фильтра.

`geo.distance` Функция также может использоваться в [ **$orderby** параметр](search-query-odata-orderby.md) сортировать результаты поиска, расстояние от заданной точки. Синтаксис для `geo.distance` в **$orderby** такой же, как и в **$filter**. При использовании `geo.distance` в **$orderby**, должен иметь тип поля, к которому он применяется `Edm.GeographyPoint` и он должен также быть **сортируемого**.

## <a name="syntax"></a>Синтаксис

Следующие EBNF ([расширенная форма Бэкуса-Наура](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) определяет грамматику `geo.distance` и `geo.intersects` функции, а также геопространственные значения, на которых они работают:

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

Также доступна схему интерактивный синтаксис:

> [!div class="nextstepaction"]
> [Диаграмма синтаксиса OData для службы поиска Azure](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> См. в разделе [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md) для завершения EBNF.

### <a name="geodistance"></a>Geo.Distance

`geo.distance` Функция принимает два параметра типа `Edm.GeographyPoint` и возвращает `Edm.Double` значение расстояния между ними в километрах. Это отличается от других служб, поддерживающих геопространственные операции OData, которые обычно возвращают расстояние в метрах.

Один из параметров для `geo.distance` должен быть константой точки geography и других должен быть путь к полю (или переменную диапазона в случае фильтр прохода поле типа `Collection(Edm.GeographyPoint)`). Порядок этих параметров не имеет значения.

Константа точки geography имеет форму `geography'POINT(<longitude> <latitude>)'`, в которых долготу и широту являются числовыми константами.

> [!NOTE]
> При использовании `geo.distance` в фильтре, необходимо сравнить расстояние, возвращаемый функцией с констант с помощью `lt`, `le`, `gt`, или `ge`. Операторы `eq` и `ne` не предназначены для сравнения расстояний. Например, это правильное применение атрибута `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>Geo.intersects

`geo.intersects` Функция принимает переменной типа `Edm.GeographyPoint` и константой `Edm.GeographyPolygon` и возвращает `Edm.Boolean`  --  `true` Если точка находится в границах элемента «многоугольник», `false` в противном случае.

Многоугольник представляет собой двухмерную поверхность, хранимую в виде последовательности точек, определяющих кольцо (см. в разделе [примеры](#examples) ниже). Многоугольник должен быть замкнут, то есть первая и конечная точки должны совпадать. [Точки многоугольника должны наноситься в порядке против часовой стрелки](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Геопространственные запросы и многоугольники, охватывающие 180th меридиана

Для многих геопространственные запроса составление запроса, включающего 180th меридиана (рядом с правым Меридиан) библиотеки является сложно или требуется обойти это ограничение, такие как разделение многоугольника на две, одному с двух сторон меридиана.

В службе поиска Azure, геопространственные запросы, включающие долготы 180 градусов сработает как ожидается, если форма запроса представляет собой прямоугольник, а координаты Выровнят макет сетки вдоль широту и долготу (например, `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). В противном случае для непрямоугольных или невыровненных фигур используйте подход с разделенным многоугольником.  

### <a name="geo-spatial-functions-and-null"></a>Геопространственные функции и `null`

Все остальные поля не являющихся коллекциями в службе поиска Azure поля типа, такие как `Edm.GeographyPoint` может содержать `null` значения. Когда служба поиска Azure определяет `geo.intersects` для поля, которое является `null`, всегда будет возвращаться результат `false`. Поведение `geo.distance` в данном случае зависит от контекста:

- В фильтрах `geo.distance` из `null` поле результатов в `null`. Это означает, что документ не будут совпадать, так как `null` по сравнению с любое ненулевое значение, результатом которого является `false`.
- При сортировке результатов с помощью **$orderby**, `geo.distance` из `null` поле приводит возможных максимальное расстояние. Документов с помощью такого поля ниже, чем все остальные сортировки при направление сортировки `asc` — использовать (по умолчанию) и больше, чем все остальные, если задано направление `desc`.

## <a name="examples"></a>Примеры

### <a name="filter-examples"></a>Примеры выражений фильтрации

Найти все гостиницы в пределах 10 километров от заданной контрольной точки (где расположение является полем типа `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Найти все гостиницы в пределах указанной области просмотра, описанной как многоугольник (где расположение является полем типа `Edm.GeographyPoint`). Обратите внимание, что многоугольник замкнут (первая и последняя точки должны совпадать, и [точки должны быть нанесены в порядке против часовой стрелки](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Примеры выражений упорядочивания

Сортировать по убыванию гостиницы `rating`, затем по возрастанию, расстояние от точки, заданной координатами:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Сортировать гостиницы, в убывающем порядке по `search.score` и `rating`, а затем в порядке возрастания значений расстояние от точки, заданной координатами таким образом, чтобы между двумя гостиницы с идентичными оценки ближайшая указывается в первую очередь:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Дальнейшие действия  

- [Фильтры в службе "Поиск Azure"](search-filters.md)
- [Общие сведения о языках выражений OData для службы поиска Azure](query-odata-filter-orderby-syntax.md)
- [Справочник по синтаксису выражений OData для службы поиска Azure](search-query-odata-syntax-reference.md)
- [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск по документам (REST API службы "Поиск Azure"))
