---
title: Добавление пузырькового слоя в карту | Карты Microsoft Azure
description: В этой статье вы узнаете, как добавить пузырьковый слой на карту с помощью веб-пакета SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 462d820824ad6c53ad4b93ad5c88c66128619467
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933664"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Добавление слоя пузырьков на карту

В этой статье показано, как визуализировать данные точек из источника данных в виде пузырькового слоя на карте. Слои пузырьков преобразовывают точки в круги на карте с помощью фиксированного значения радиуса в пикселях. 

> [!TIP]
> Слои пузырьков по умолчанию отображают координаты всех геометрических объектов в источнике данных. Чтобы ограничить слой таким образом, чтобы он отображал только возможности геометрических точек, задайте для свойства `filter` слоя значение `['==', ['geometry-type'], 'Point']` или `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`, если требуется также включить компоненты MultiPoint.

## <a name="add-a-bubble-layer"></a>Добавление слоя пузырьков

Следующий код загружает массив точек в источник данных. Затем точки данных соединяются с [пузырьковым слоем](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest). Пузырьковый слой отображает радиус каждого пузырька с пятью пикселями, цвет заливки белого цвета, цвет обводки синего цвета и толщину штриха в 6 пикселях. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Ниже приведен полный пример выполнения кода описанной выше функциональности.

<br/>

<iframe height='500' scrolling='no' title='Источник данных BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>Источник данных BubbleLayer</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Отображение меток с помощью слоя пузырьков

В следующем коде показано, как использовать пузырьковый слой для отрисовки точки на карте и слоя символов для отрисовки метки. Чтобы скрыть значок слоя символов, задайте для свойства `image` параметров значка значение `'none'`.

<br/>

<iframe height='500' scrolling='no' title='Источник данных MultiLayer' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>Источник данных MultiLayer</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Настройка слоя пузырьков

Слой пузырьков имеет несколько параметров стиля, которые можно опробовать с помощью представленного ниже средства.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя пузырьков' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Параметры слоя пузырьков</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Создание источника данных](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавление слоя символов](map-add-pin.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)
