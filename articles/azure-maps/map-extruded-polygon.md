---
title: Добавление слоя объема фигуры в карту | Карты Microsoft Azure
description: Добавление слоя объемной фигуры в веб-пакет SDK Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334547"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Добавление слоя объема фигуры многоугольника к карте

В этой статье показано, как использовать слой объемного `Polygon` многоугольника для отрисовки областей `MultiPolygon` и геометрических объектов в виде вытянутых фигур. Azure Maps веб-пакет SDK поддерживает отрисовку круговых геометрических объектов, как определено в [расширенной схеме геоjson](extend-geojson.md#circle). Эти круги можно преобразовать в многоугольники при отображении на карте. Все геометрические функции могут быть легко обновлены при работе с [Atlas. Класс Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

## <a name="use-a-polygon-extrusion-layer"></a>Использование уровня объемной фигуры многоугольника

Соедините [слой объема многоугольников](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) с источником данных. Затем загрузили его на карту. Слой объемов многоугольников отображает области компонентов `Polygon` и `MultiPolygon` в виде вытянутых фигур. Свойства `height` и `base` уровня объема фигуры многоугольника определяют базовое расстояние от земли и высоты вытянутой фигуры в **метрах**. В следующем коде показано, как создать многоугольник, добавить его в источник данных и подготовить к просмотру с помощью класса уровня объема многоугольников.

> [!Note]
> `base` Значение, определенное на уровне объема многоугольника, должно быть меньше или равно значению объекта `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Вытянутый многоугольник" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>затягивание</a> пера на Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Добавить управляемые данными многоугольники

Карту хороплет можно визуализировать с помощью слоя объема фигуры многоугольника. Установите свойства `height` и `fillColor` уровня объемной фигуры в измерение статистической переменной в геометрических `Polygon` компонентах `MultiPolygon` и. В следующем образце кода показана вытянутая хороплетная схема U. S, основанная на измерении плотности Генеральной совокупности по штату.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Вытянутая хороплетная схема" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. <a href='https://codepen.io/azuremaps/pen/eYYYNox'>хороплет карту с вытягиванием</a> пера по<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Добавление окружности на карту

Azure Maps использует расширенную версию схемы геоjson, которая предоставляет определение для кругов, как указано [здесь](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). Вытянутый круг может быть визуализирован на карте путем создания `point` функции со `subType` свойством `Circle` и пронумерованным `Radius` свойством, представляющим радиус в **метрах**. Пример:

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

Azure Maps веб-пакет SDK преобразует `Point` эти функции `Polygon` в функции, изнутри. Эти `Point` функции можно визуализировать на карте, используя уровень объемного многоугольника, как показано в следующем примере кода.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Помощью Дронов, многоугольник "airspace"" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел Pen <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>помощью Дронов "airspace</a> " с<a href='https://codepen.io/azuremaps'>@azuremaps</a>помощью Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Настройка уровня объемной фигуры многоугольника

Уровень объема многоугольников имеет несколько параметров стилей. которые можно опробовать с помощью представленного ниже средства.

<br/>

<iframe height='700' scrolling='no' title='пугбрж' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>Пугбрж</a> пера by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Многоугольник](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [уровень объемной фигуры многоугольника](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Дополнительные ресурсы:

> [!div class="nextstepaction"]
> [Расширение спецификации геоjson Azure Maps](extend-geojson.md#circle)
