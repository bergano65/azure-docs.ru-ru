---
title: Добавление слоя объема фигуры в карту | Карты Microsoft Azure
description: Добавление слоя объемной фигуры в веб-пакет SDK Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: eedbbc0126adacc2a9bdc151aa6dbc27c7ba0750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310260"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Добавление слоя объема фигуры многоугольника к карте

В этой статье показано, как использовать слой объемного многоугольника для отрисовки областей `Polygon` и `MultiPolygon` геометрических объектов в виде вытянутых фигур. Azure Maps веб-пакет SDK поддерживает отрисовку круговых геометрических объектов, как определено в [расширенной схеме геоjson](extend-geojson.md#circle). Эти круги можно преобразовать в многоугольники при отображении на карте. Все геометрические функции могут быть легко обновлены при работе с [Atlas. Класс Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) .

## <a name="use-a-polygon-extrusion-layer"></a>Использование уровня объемной фигуры многоугольника

Соедините [слой объема многоугольников](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer) с источником данных. Затем загрузили его на карту. Слой объемов многоугольников отображает области `Polygon` компонентов и в `MultiPolygon` виде вытянутых фигур. `height`Свойства и `base` уровня объема фигуры многоугольника определяют базовое расстояние от земли и высоты вытянутой фигуры в **метрах**. В следующем коде показано, как создать многоугольник, добавить его в источник данных и подготовить к просмотру с помощью класса уровня объема многоугольников.

> [!Note]
> `base`Значение, определенное на уровне объема многоугольника, должно быть меньше или равно значению объекта `height` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Вытянутый многоугольник" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
См. <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>затягивание</a> пера на Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) в <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Добавить управляемые данными многоугольники

Карту хороплет можно визуализировать с помощью слоя объема фигуры многоугольника. Установите `height` Свойства и `fillColor` уровня объемной фигуры в измерение статистической переменной в `Polygon` `MultiPolygon` геометрических компонентах и. В следующем образце кода показана вытянутая хороплетная схема U. S, основанная на измерении плотности Генеральной совокупности по штату.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Вытянутая хороплетная схема" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
См. <a href='https://codepen.io/azuremaps/pen/eYYYNox'>хороплет карту с вытягиванием</a> пера по Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Добавление круга на карту

Azure Maps использует расширенную версию схемы геоjson, которая предоставляет определение для кругов, как указано [здесь](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). Вытянутый круг может быть визуализирован на карте путем создания `point` функции со `subType` свойством `Circle` и пронумерованным `Radius` свойством, представляющим радиус в **метрах**. Пример:

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps веб-пакет SDK преобразует эти `Point` функции в `Polygon` функции, изнутри. Эти `Point` функции можно визуализировать на карте, используя уровень объемного многоугольника, как показано в следующем примере кода.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Помощью Дронов, многоугольник "airspace"" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
См. раздел Pen <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>помощью Дронов "airspace</a> " с помощью Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Настройка уровня объемной фигуры многоугольника

Уровень объема многоугольников имеет несколько параметров стилей. которые можно опробовать с помощью представленного ниже средства.

<br/>

<iframe height='700' scrolling='no' title='пугбрж' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>Пугбрж</a> пера by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Многоугольник](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [уровень объемной фигуры многоугольника](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer)

Дополнительные ресурсы:

> [!div class="nextstepaction"]
> [Расширение спецификации GeoJSON Azure Maps](extend-geojson.md#circle)
