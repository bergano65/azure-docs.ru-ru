---
title: Создание источника данных в Azure Maps | Документация Майкрософт
description: Создание источника данных и его использование с Azure Maps веб-пакетом SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 36c06182d0807ce3d255477a865023ae7b74e2cb
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874929"
---
# <a name="create-a-data-source"></a>Создание источника данных

Azure Maps веб-пакет SDK хранит данные в источниках данных, которые оптимизируют данные для запросов и подготовки к просмотру. В настоящее время существует два типа источников данных:

**Источник данных геоjson**

Источник данных на основе геообъектного формата JSON может загружать и хранить данные `DataSource` локально с помощью класса. Данные геоjson можно создавать или создавать вручную с помощью вспомогательных классов в пространстве имен [Atlas. Data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . `DataSource` Класс предоставляет функции для импорта локальных и удаленных геоjson файлов. Удаленные геоjson-файлы должны размещаться на конечной точке с поддержкой CORs. `DataSource` Класс предоставляет функциональные возможности для данных точки кластеризации. Данные можно легко добавлять, удалять и обновлять с помощью `DataSource` класса.


> [!TIP]
> Если вы хотите перезаписать все данные в `DataSource`, то при вызове `clear` функций then `add` эта схема попытается повторно выполнить визуализацию дважды, что может привести к некоторой задержке. Вместо этого используйте `setShapes` функцию, которая удалит и заменит все данные в источнике данных и запустит только одну повторную визуализацию схемы.

**Источник плитки вектора**

Источник векторной плитки описывает доступ к слою векторной плитки и может быть создан с помощью класса [вектортилесаурце](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) . Azure Maps соответствует [спецификации плитки вектора мапбокс](https://github.com/mapbox/vector-tile-spec), которая является открытым стандартом. Слои векторной мозаики похожи на мозаичные слои, а не на мозаичное изображение, а в виде сжатого файла (формат ПБФ), который содержит данные карты вектора, и одного или нескольких слоев, которые могут быть визуализированы и применены на стороне клиента на основе стиля каждого слоя. Данные в векторной плитке содержат географические функции в виде точек, линий и многоугольников. Есть несколько преимуществ векторных мозаичных слоев над растровыми слоями.

 - Размер файла плитки вектора обычно намного меньше, чем эквивалентная растровая плитка. Поэтому используется меньшая пропускная способность, что означает меньшую задержку и более быструю карту. Это повышает удобство работы пользователей.
 - Так как векторные плитки подготавливаются к просмотру на стороне клиента, они могут адаптироваться к разрешению устройства, на котором они отображаются. Это делает возможным отображение отображаемых карт, которые более четко определяются и имеют метки Crystal Clear. 
 - Изменение стиля данных в картах вектора не требует повторной загрузки данных, так как на клиенте можно применить новый стиль. В отличие от этого, изменение стиля растрового слоя, как правило, требует загрузки плиток с сервера, к которым применен новый стиль.
 - Поскольку данные доставляются в виде векторной формы, для подготовки данных требуется меньше обработки на стороне сервера, что означает, что более новые данные можно сделать более доступными.

Все слои, использующие Векторный источник, должны указывать `sourceLayer` значение. 

После создания источники данных можно добавить на карту с помощью `map.sources` свойства, которое является [саурцеманажер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). В следующем коде показано, как создать объект `DataSource` и добавить его в карту.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>Подключение источника данных к слою

Данные подготавливаются к просмотру на карте с помощью слоев подготовки отчетов. Один или несколько уровней подготовки отчетов могут ссылаться на один источник данных. Для следующих слоев подготовки отчетов требуется, чтобы источник данных был мощным.

- [Пузырьковый слой](map-add-bubble-layer.md) — отображает данные точек в виде масштабируемых кругов на карте.
- [Слой символов](map-add-pin.md) — отображает данные точек в виде значков и (или) текста.
- [Уровень тепловой карт](map-add-heat-map-layer.md) — отрисовывает данные точек в виде плотной тепловой карте.
- [Слой линии](map-add-shape.md) — может использоваться для отрисовки линии и или контура многоугольников. 
- [Слой многоугольников](map-add-shape.md) . Заливка области многоугольника сплошным цветом или узором изображения.

В следующем коде показано, как создать источник данных, добавить его к карте и подключить его к пузырьковой слою, а затем импортировать данные географической точки JSON из удаленного расположения в него. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Существуют дополнительные слои подготовки к просмотру, которые не подключаются к этим источникам данных, а загружают данные, которые они отображают напрямую. 

- [Слой изображения](map-add-image-layer.md) — наложение одного изображения поверх схемы и привязка его углов к набору заданных координат.
- [Мозаичный слой](map-add-tile-layer.md) . переносит растровый слой мозаики поверх карты.

## <a name="one-data-source-with-multiple-layers"></a>Один источник данных с несколькими слоями

К одному источнику данных можно подключить несколько слоев. Это может показаться нечетным, но существует множество различных сценариев, в которых это полезно. Возьмем, к примеру, сценарий создания географических интерфейсов многоугольников. Когда пользователь рисует многоугольник, необходимо визуализировать область заполнения многоугольников при добавлении точек на карту. Добавление линии в стиле, обрисованной многоугольником, упрощает просмотр краев многоугольника при его прорисовке. Наконец, Добавление некоторого типа маркера, такого как ПИН-код или маркер, над каждой позицией многоугольника облегчит редактирование каждой отдельной должности. Ниже приведен образ, демонстрирующий этот сценарий.

![Схема, отображающая несколько слоев данные для отрисовки из одного источника данных](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Для выполнения этого сценария в большинстве платформ сопоставления необходимо создать объект Polygon, объект Line и закрепить каждое расположение на многоугольнике. После изменения многоугольника необходимо вручную обновить линию и ПИН-коды, что может быстро стать сложным.

В Azure Maps все, что вам нужно, — это один многоугольник в источнике данных, как показано в приведенном ниже коде.

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

## <a name="next-steps"></a>Следующие шаги

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
> [Добавить слой линий](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](map-add-shape.md)

> [!div class="nextstepaction"]
> [Добавление тепловой карт](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)