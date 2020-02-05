---
title: Добавление слоя объема фигуры в карту | Карты Microsoft Azure
description: Добавление слоя объемной фигуры в веб-пакет SDK Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f01e07ea2bbfd0f6b3b0cc19dd219d71984a0d45
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988572"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Добавление слоя объема фигуры многоугольника к карте

В этой статье показано, как использовать слой объемного многоугольника для отображения областей `Polygon` и `MultiPolygon` геометрических объектов в виде вытянутых фигур. Azure Maps веб-пакет SDK поддерживает отрисовку круговых геометрических объектов, как определено в [расширенной схеме геоjson](extend-geojson.md#circle). Эти круги можно преобразовать в многоугольники при отображении на карте. Все геометрические функции могут быть легко обновлены при работе с [Atlas. Класс Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

## <a name="use-a-polygon-extrusion-layer"></a>Использование уровня объемной фигуры многоугольника

Соедините [слой объема многоугольников](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) с источником данных. Затем загрузили его на карту. Слой объемов многоугольников отображает области `Polygon` и `MultiPolygon` функций в виде вытянутых фигур. Свойства `height` и `base` слоя уровня "уровень" определяют базовое расстояние от нуля и высоты вытянутой фигуры в **метрах**. В следующем коде показано, как создать многоугольник, добавить его в источник данных и подготовить к просмотру с помощью класса уровня объема многоугольников.

> [!Note]
> Значение `base`, определенное на уровне объема фигуры, должно быть меньше или равно значению `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Вытянутый многоугольник" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Ознакомьтесь с <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>вытягиванием</a> пера на Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Добавить управляемые данными многоугольники

Карту хороплет можно визуализировать с помощью слоя объема фигуры многоугольника. Задайте для свойств `height` и `fillColor` уровня объемной фигуры измерение статистической переменной в `Polygon` и `MultiPolygon` геометрические объекты. В следующем образце кода показана вытянутая хороплетная схема U. S, основанная на измерении плотности Генеральной совокупности по штату.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Вытянутая хороплетная схема" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. <a href='https://codepen.io/azuremaps/pen/eYYYNox'>хороплет карту с вытягиванием</a> пера по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Добавление окружности к карте

Azure Maps использует расширенную версию схемы геоjson, которая предоставляет определение для кругов, как указано [здесь](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). Вытянутый круг можно визуализировать на карте, создав `point`ную функцию со свойством `subType` `Circle` и числовым свойством `Radius`, представляющим радиус в **метрах**. Пример.

```Javascript
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

Azure Maps веб-пакет SDK преобразует эти `Point` функции в `Polygon` функции внутри. Эти `Point` функции можно визуализировать на карте, используя уровень объемного многоугольника, как показано в следующем примере кода.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Помощью Дронов, многоугольник "airspace"" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел Pen <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>помощью Дронов "airspace</a> " с Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Настройка уровня объемной фигуры многоугольника

Уровень объема многоугольников имеет несколько параметров стилей. которые можно опробовать с помощью представленного ниже средства.

<br/>

<iframe height='700' scrolling='no' title='пугбрж' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>Пугбрж</a> пера by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Многоугольник](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [уровень объемной фигуры многоугольника](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Дополнительные ресурсы:

> [!div class="nextstepaction"]
> [Расширение спецификации геоjson Azure Maps](extend-geojson.md#circle)
