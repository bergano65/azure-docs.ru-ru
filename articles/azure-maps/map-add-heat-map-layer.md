---
title: Добавление слоя тепловой карты в Azure Maps | Документация Майкрософт
description: How to add a heat map layer to the Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f7115e7c8b95efd0e3bbc8a788528878c2d1f092
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484309"
---
# <a name="add-a-heat-map-layer"></a>Добавление слоя тепловой карты

Тепловая карта, которую также называют картой точек плотности, это тип визуализации данных для представления плотности данных с помощью диапазона цветов. Такие визуализации часто используются для отображения "горячих точек" данных на карте, и они особенно полезны, чтобы преобразовывать для просмотра набор данных больших точек.  For example, rendering tens of thousands of points within the map view as symbols, covers most of the map area and would result in many symbols overlapping each other, making it difficult to gain much insight into the data. Если же те же данные будут отображаться на тепловой карте, вы сможете легко заметить области максимальной или относительно низкой плотности относительно других областей карты. Тепловые карты используются в разных сценариях. Вот лишь несколько примеров.

- В формате тепловой карты очень удобно отображать данные о температурах, так как она предоставляет приблизительные значения данных между двумя точками.
- Отображение сведений от датчиков шума в виде тепловой карты не только позволяет оценить уровень шума в точке установки датчика, но и дает некоторое представление о снижении шума на расстоянии. Уровень шума на одном сайте относительно невелик, но близкое расположение таких областей повышает уровень шума в датчиках перекрытия. Возможно, что область с перекрытием имеет более высокие уровни чувствительности к шуму, и таким образом они появятся на тепловой карте.
- Visualizing a GPS trace that includes the speed as a weighted height map where the intensity of each data point is based on the speed is a great way to see where the vehicle was speeding.

> [!TIP]
> Слои тепловой карты по умолчанию преобразовывают координаты всех геометрических объектов в источнике данных. To limit the layer so that it only renders point geometry features, set the `filter` property of the layer to `['==', ['geometry-type'], 'Point']` or `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` if you want to include MultiPoint features as well.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Добавление слоя тепловой карты

Чтобы преобразовать источник данных точек в виде тепловой карты, передайте этот источник данных в экземпляр класса `HeatMapLayer` и добавьте его на карту, как показано в следующем примере.

In the following code, each heat point has a radius of 10 pixels at all zoom levels. When adding the heat map layer to the map, this sample inserts it below the label layer to create a better user experience as the labels are clearly visible above the heat map. The data in this sample is sourced from the [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) and represents significant earthquakes that have occurred in the last 30 days.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a data set of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Below is the complete running code sample of the above functionality.

<br/>

<iframe height='500' scrolling='no' title='Простой слой тепловой карты' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>простого слоя тепловой карты</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в репозитории <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-the-heat-map-layer"></a>Настройка слоя тепловой карты

В предыдущем примере настройка тепловой карты заключалась в установке параметров радиуса и прозрачности. Слой тепловой карты предоставляет несколько параметров для настройки.

* `radius`: Defines a pixel radius in which to render each data point. Радиус определяется как фиксированное число или выражение. Using an expression, it's possible to scale the radius based on the zoom level, that appears to represent a consistent spatial area on the map (for example, 5-mile radius).
* `color`: Specifies how the heat map is colorized. A color gradient is often used for heat maps and can be achieved with an `interpolate` expression. Using a `step` expression for colorizing the heat map breaks up the density visually into ranges that more so resembles a contour or radar style map. Эти цветовые палитры определяют цвета в диапазоне от минимальной до максимальной плотности. Значения цветов для тепловых карт указываются в виде выражения для значения `heatmap-density`. The color at index 0 in an interpolation expression or the default color of a step expression, defines the color of the area where there's no data and can be used to define a background color. Многие пользователи предпочитают использовать для этого прозрачный или полупрозрачный черный цвет. Вот несколько примеров выражений для определения цвета.

| Interpolation Color Expression | Выражение для ступенчатой палитры | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] | 

- `opacity`: Specifies how opaque or transparent the heat map layer is.
- `intensity`: Applies a multiplier to the weight of each data point to increase the overall intensity of the heatmap and helps to make the small differences in the weight of data points become easier to visualize.
- `weight`: By default, all data points have a weight of 1, thus all data points are weighted equally. Параметр weight используется в качестве множителя. Для него можно использовать фиксированное число или выражение. Если в параметре weight используется число, например 2, плотность каждой точки удваивается, как если бы она была нанесена на карту дважды. Фиксированное число в качестве значения параметра weight изменяет тепловую карту точно так же, как изменение параметра intensity. However, if an expression is used, the weight of each data point can be based on the properties of each data point. Например, в данных землетрясения каждая точка данных представляет землетрясение. Важная метрика в каждой точке данных землетрясения — это значение величины. Землетрясения происходят постоянно, но большинство из них имеют низкую магнитуду и практически не заметны. Using the magnitude value in an expression to assign the weight to each data point will allow more significant earthquakes to be better represented within the heat map.
- Кроме параметров базового слоя, минимального и максимального масштаба, видимости и фильтра, есть еще параметр `source`, который позволяет обновлять источник данных и параметр `source-layer`, если источник данных является источником векторных фрагментов.

Это средство позволяет проверить разные настройки для слоя тепловой карты.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя тепловой карты' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода с <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>параметрами слоя тепловой карты</a> для службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>), размещенный в репозитории <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Consistent zoomable heat map

By default, the radii of data points rendered in the heat map layer have a fixed pixel radius for all zoom levels. As the map is zoomed, the data aggregates together and the heat map layer looks different. A `zoom` expression can be used to scale the radius for each zoom level such that each data point covers the same physical area of the map. This will make the heat map layer look more static and consistent. Each zoom level of the map has twice as many pixels vertically and horizontally as the previous zoom level. Scaling the radius such that it doubles with each zoom level will create a heat map that looks consistent on all zoom levels. This can be accomplished by using the `zoom` with a base 2 `exponential interpolation` expression as shown in the sample below. Zoom the map to see how the heat map scales with the zoom level.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Consistent zoomable heat map" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
See the Pen <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>Consistent zoomable heat map</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> При включенной кластеризации для источника данных, расположенные близко друг к другу точки данных группируются в кластеризованые точки. Число точек в каждом кластере можно использовать в выражении веса для тепловой карты. Такой подход позволяет значительно сократить число отображаемых точек. The point count of a cluster is stored in a `point_count` property of the point feature as shown below. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Если радиус кластеризации составляет несколько пикселей, визуальные различия будут минимальными. Больший радиус означает группирование большего числа точек в каждом кластере, что повысит производительность тепловой карты, но одновременно создаст более заметные визуальные различия.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Create a data source](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)