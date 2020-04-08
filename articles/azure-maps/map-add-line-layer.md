---
title: Добавить линейный слой на карту Карты Microsoft Azure
description: В этой статье вы узнаете, как добавить линейный слой к карте с помощью веб-SDK Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c473be25907eb3a761fbccd598bb9b732e5be5b9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802354"
---
# <a name="add-a-line-layer-to-the-map"></a>Добавление слоя линии на карту

Слой линии может использоваться для визуализации `LineString` и `MultiLineString` функций как путей или маршрутов на карте. Слой линии также может быть использован `Polygon` `MultiPolygon` для визуализации контуров и объектов. Источник данных подключен к линейной слою, чтобы предоставить ему данные для визуализации. 

> [!TIP]
> Слои строк по умолчанию будут отображать координаты полигонов, а также строки в источнике данных. Чтобы ограничить слой таким, чтобы он только `filter` отображал `['==', ['geometry-type'], 'LineString']` `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` функции LineString, устанавливали свойство слоя или если вы хотите включить функции MultiLineString.

Следующий код показывает, как создать строку. Добавьте строку в исходный исход данных, а затем свизуйте ее с линейным слоем с помощью класса [LineLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Ниже приводится полный выборка кода приведена вышеуказанной функциональности.

<br/>

<iframe height='500' scrolling='no' title='Добавление линии на карту' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Добавление линии на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

Слои строкможно стилизуть с помощью [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) и [использовать выражения стиля, управляемые данными.](data-driven-style-expressions-web-sdk.md)

## <a name="add-symbols-along-a-line"></a>Добавление символов вдоль строки

В этом примере показано, как добавить значки стрелки вдоль линии на карте. При использовании слоя символа установите опцию "размещение" на "линию". Эта опция будет отображать символы вдоль линии и поворачивать значки (0 градусов и справа).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Показать стрелку вдоль линии" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/drBJwX/'>стрелка Pen Show вдоль линии</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Веб-SDK Azure Maps предоставляет несколько настраиваемых шаблонов изображений, которые можно использовать со слоем символов. Для получения дополнительной информации смотрите документ [«Как использовать шаблоны изображений».](how-to-use-image-templates-web-sdk.md)

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Добавление градиента штриха к строке

Вы можете применить один цвет штриха к линии. Вы также можете заполнить линию с градиентом цветов, чтобы показать переход от сегмента одной линии к следующему сегменту линии. Например, градиенты строкмогут использоваться для представления изменений времени и расстояния или различных температур по подключенной линии объектов. Для того, чтобы применить эту функцию к `lineMetrics` строке, источник данных должен иметь набор опции, чтобы сделать это, а затем выражение градиента цвета может быть передано варианту `strokeColor` строки. Выражение градиента штриха `['line-progress']` должно ссылаться на выражение данных, которое подвергает вычисленной линии метрикам выражения.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Линия с градиентом инсульта" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите строку с <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>градиентом Инсульта</a> по Картам Azure ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Настройка слоя линий

Слой Line имеет несколько вариантов укладки. которые можно опробовать с помощью представленного ниже средства.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя линий' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода с <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>параметрами слоя линий</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Создание источника данных](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавление всплывающего окна](map-add-popup.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Использование шаблонов образов](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](map-add-shape.md)
