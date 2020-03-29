---
title: Создание источника данных для карты Карты Microsoft Azure
description: В этой статье вы узнаете, как создать источник данных и добавить его на карту с помощью Веб-SDK Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190233"
---
# <a name="create-a-data-source"></a>Создание источника данных

Веб-SDK Azure Maps хранит данные в источниках данных. Использование источников данных оптимизирует операции данных для запросов и визуализации. В настоящее время существует два типа источников данных:

**Источник данных GeoJSON**

На основе GeoJSON источник данных загружает и хранит данные локально с помощью `DataSource` класса. Данные GeoJSON могут создаваться вручную или создаваться с помощью классов помощников в пространстве имен [atlas.data.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) Класс `DataSource` предоставляет функции для импорта локальных или удаленных файлов GeoJSON. Удаленные файлы GeoJSON должны размещаться на конечную точку с включенной КОРам. Класс `DataSource` предоставляет функциональность для данных точек кластеризации. Кроме того, данные можно легко добавлять, удалять и обновлять вместе с классом. `DataSource`


> [!TIP]
> Допустим, вы хотите перезаписать `DataSource`все данные в . Если вы делаете `clear` вызовы на тогдашние `add` функции, карта может повторно сделать дважды, что может привести к небольшой задержке. Вместо этого `setShapes` используйте функцию, которая будет удалять и заменять все данные в источнике данных и вызывать только один ререндеринг карты.

**Источник векторной плитки**

Источник векторной плитки описывает, как получить доступ к векторной плитке слоя. Используйте класс [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) для мгновенного развития источника векторной плитки. Слои векторной плитки похожи на слои плитки, но они не одинаковы. Слой плитки представляет собой изображение raster. Векторные слои плитки — это сжатый файл в формате PBF. Этот сжатый файл содержит данные карты переносчиков и один или несколько слоев. Файл может быть отрисован и стилизован под клиента, в зависимости от стиля каждого слоя. Данные в векторной плитке содержат географические объекты в виде точек, линий и полигонов. Есть несколько преимуществ использования векторных слоев плитки вместо слоев плитки raster:

 - Размер файла векторной плитки, как правило, намного меньше, чем эквивалентная raster плитка. Таким образом, используется меньшая пропускная способность. Это означает более низкую задержку, более быструю карту и лучший пользовательский опыт.
 - Поскольку векторные плитки отображаются на клиенте, они адаптируются к разрешению устройства, на которое они отображаются. В результате отображаемые карты выглядят более четко определенными, с кристально чистыми метками.
 - Изменение стиля данных на векторных картах не требует повторной загрузки данных, так как новый стиль может быть применен к клиенту. В отличие от этого, изменение стиля слоя плитки raster обычно требует загрузки плитки с сервера, а затем применения нового стиля.
 - Поскольку данные поставляются в векторной форме, для подготовки данных требуется меньше обработки сервера. В результате новые данные могут быть доступны быстрее.

Все слои, использовавающие источник переносчика, `sourceLayer` должны указывать значение.

После создания, источники данных могут `map.sources` быть добавлены на карту через свойство, которое является [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Следующий код показывает, `DataSource` как создать и добавить его на карту.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure Maps придерживается [спецификации плитки Вектора Mapbox](https://github.com/mapbox/vector-tile-spec), открытого стандарта.

## <a name="connecting-a-data-source-to-a-layer"></a>Подключение источника данных к слою

Данные отображаются на карте с помощью отрисованных слоев. На один источник данных можно ссылаться одним или несколько слоями рендеринга. Следующие слои рендеринга требуют источника данных:

- [Слой пузыря](map-add-bubble-layer.md) - отображает точечные данные как масштабированные круги на карте.
- [Слой символа](map-add-pin.md) - отображает точечные данные как значки или текст.
- [Слой тепловой карты](map-add-heat-map-layer.md) - отображает точечные данные как карту плотности тепла.
- [Слой строки](map-add-shape.md) - визуализация линии и или визуализация контура полигонов. 
- [Слой полигона](map-add-shape.md) - заполняет площадь полигона твердым цветом или изображением.

Следующий код показывает, как создать источник данных, добавить его на карту и подключить к пузырчатому слою. Затем импортируйте данные точек GeoJSON из удаленного местоположения в источник данных. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Есть дополнительные слои рендеринга, которые не подключаются к этим источникам данных, но они непосредственно загружают данные для рендеринга. 

- [Слой изображения](map-add-image-layer.md) - накладывает одно изображение поверх карты и связывает его углы с набором указанных координат.
- [Слой плитки](map-add-tile-layer.md) - накладывает слой плитки raster на верхней части карты.

## <a name="one-data-source-with-multiple-layers"></a>Один источник данных с несколькими слоями

Несколько слоев могут быть подключены к одному источнику данных. Существует множество различных сценариев, в которых этот вариант полезен. Например, рассмотрим сценарий, в котором пользователь рисует полигон. Мы должны сделать и заполнить область полигона, как пользователь добавляет точки на карте. Добавление стилизованной линии для набросков полигона облегчает понимание краев полигона по мере рисования пользователем. Чтобы удобно отсевать отдельное положение в полигоне, мы можем добавить ручку, как булавку или маркер, над каждой позицией.

![Карта, показывающая несколько слоев рендеринга данных из одного источника данных](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

В большинстве платформ для картирования потребуется многоугольный объект, линейный объект и штифт для каждой позиции в полигоне. Поскольку полигон модифицирован, необходимо вручную обновить линию и булавки, которые могут быстро стать сложными.

С Azure Maps все, что вам нужно, это один полигон в источнике данных, как показано в приведенном ниже коде.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Datasource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [ВекторТильИсточник](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление всплывающего окна](map-add-popup.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавление слоя символов](map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавить слой пузыря](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя линий](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](map-add-shape.md)

> [!div class="nextstepaction"]
> [Добавление тепловой карты](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)