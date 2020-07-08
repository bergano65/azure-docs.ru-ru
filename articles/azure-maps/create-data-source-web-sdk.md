---
title: Создание источника данных для схемы | Карты Microsoft Azure
description: В этой статье вы узнаете, как создать источник данных и добавить его в карту с помощью веб-пакета SDK для карт Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 7c23e659463364c5e1a497ead138abb4c696627a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207504"
---
# <a name="create-a-data-source"></a>Создание источника данных

Azure Maps веб-пакет SDK хранит данные в источниках данных. Использование источников данных оптимизирует операции с данными для выполнения запросов и подготовки к просмотру. В настоящее время существует два типа источников данных:

**Источник данных геоjson**

Источник данных на основе геообъектного формата JSON загружает и сохраняет данные локально с помощью `DataSource` класса. Данные геоjson можно создавать или создавать вручную с помощью вспомогательных классов в пространстве имен [Atlas. Data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . `DataSource`Класс предоставляет функции для импорта локальных и удаленных геоjson файлов. Удаленные геоjson-файлы должны размещаться на конечной точке с поддержкой CORs. `DataSource`Класс предоставляет функциональные возможности для данных точки кластеризации. И можно легко добавлять, удалять и обновлять данные с помощью `DataSource` класса. В следующем коде показано, как можно создавать геообъектные данные JSON в Azure Maps.

```Javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

После создания источники данных можно добавить на карту с помощью `map.sources` свойства, которое является [саурцеманажер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). В следующем коде показано, как создать объект `DataSource` и добавить его в карту.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

В следующем коде показаны различные способы добавления данных геообъектного формата JSON в `DataSource` .

```Javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
dataSource.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
dataSource.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
dataSource.setShapes(geoJsonData);
```

> [!TIP]
> Допустим, вы хотите перезаписать все данные в `DataSource` . При вызове `clear` `add` функций then карту могут повторно отрисовываться дважды, что может привести к некоторой задержке. Вместо этого используйте `setShapes` функцию, которая удалит и заменит все данные в источнике данных и запустит только одну повторную визуализацию схемы.

**Источник плитки вектора**

Источник векторной плитки описывает, как получить доступ к слою векторной плитки. Используйте класс [вектортилесаурце](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) для создания экземпляра источника плитки вектора. Векторные мозаичные слои похожи на мозаичные слои, но они не одинаковы. Мозаичный слой — это растровое изображение. Векторные мозаичные слои — это сжатый файл в формате ПБФ. Этот сжатый файл содержит данные о векторной карте и один или несколько слоев. Файл может быть визуализирован и применен на стороне клиента в зависимости от стиля каждого слоя. Данные в векторной плитке содержат географические функции в виде точек, линий и многоугольников. Существует несколько преимуществ использования векторных слоев, а не растровых слоев мозаики.

 - Размер файла плитки вектора обычно намного меньше, чем эквивалентная растровая плитка. Поэтому используется меньшая пропускная способность. Это означает более низкую задержку, более быструю карту и удобство работы пользователей.
 - Так как векторные плитки отображаются на клиенте, они адаптируются к разрешению устройства, на котором они отображаются. В результате отображаемые карты будут более четко определены с помощью меток Crystal Clear.
 - Изменение стиля данных в картах вектора не требует повторной загрузки данных, так как на клиенте можно применить новый стиль. В отличие от этого, изменение стиля растрового слоя, как правило, требует загрузки плиток с сервера, а затем применения нового стиля.
 - Поскольку данные доставляются в виде векторной формы, для подготовки данных требуется меньше обработки на стороне сервера. В результате новые данные можно сделать доступными быстрее.

Все слои, использующие Векторный источник, должны указывать `sourceLayer` значение.

Azure Maps соответствует [спецификации плитки вектора мапбокс](https://github.com/mapbox/vector-tile-spec)— открытым стандартом.

## <a name="connecting-a-data-source-to-a-layer"></a>Подключение источника данных к слою

Данные подготавливаются к просмотру на карте с помощью слоев подготовки отчетов. Один или несколько уровней подготовки отчетов могут ссылаться на один источник данных. Для следующих слоев подготовки отчетов требуется источник данных:

- [Пузырьковый слой](map-add-bubble-layer.md) — отображает данные точек в виде масштабируемых кругов на карте.
- [Слой символов](map-add-pin.md) — отрисовывает данные точек в виде значков или текста.
- [Уровень тепловой карт](map-add-heat-map-layer.md) — отрисовывает данные точек в виде плотной тепловой карте.
- [Слой линии](map-add-shape.md) — прорисовка линии и отображение контура многоугольников. 
- [Слой многоугольников](map-add-shape.md) . Заливка области многоугольника сплошным цветом или узором изображения.

В следующем коде показано, как создать источник данных, добавить его на карту и подключить его к пузырьковой слою. Затем импортируйте данные географической точки JSON из удаленного расположения в источник данных. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Существуют дополнительные слои подготовки к просмотру, которые не подключаются к этим источникам данных, но они напрямую загружают данные для подготовки к просмотру. 

- [Слой изображения](map-add-image-layer.md) — наложение одного изображения поверх схемы и привязка его углов к набору заданных координат.
- [Мозаичный слой](map-add-tile-layer.md) . переносит растровый слой мозаики поверх карты.

## <a name="one-data-source-with-multiple-layers"></a>Один источник данных с несколькими слоями

К одному источнику данных можно подключить несколько слоев. Существует множество различных сценариев, в которых этот вариант полезен. Например, рассмотрим ситуацию, в которой пользователь рисует многоугольник. Мы должны подготовить и заполнить область многоугольника при добавлении пользователем точек на карту. Добавление линии стиля для контура многоугольник упрощает просмотр краев многоугольника по мере рисования пользователем. Чтобы изменить отдельную точку на многоугольнике, мы можем добавить маркер, например ПИН-код или маркер, над каждой позицией.

![Схема, отображающая несколько слоев данные для отрисовки из одного источника данных](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Для большинства платформ сопоставления требуется объект многоугольника, объект Line и ПИН-код для каждой из позиций многоугольника. После изменения многоугольника необходимо вручную обновить линию и ПИН-коды, что может быстро стать сложным.

В Azure Maps необходимо всего один многоугольник в источнике данных, как показано в приведенном ниже коде.

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
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [вектортилесаурце](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [вектортилесаурцеоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление всплывающего окна](map-add-popup.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавление слоя символов](map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя линий](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](map-add-shape.md)

> [!div class="nextstepaction"]
> [Добавление тепловой карты](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)