---
title: Добавление слоя многоугольников в карты Android | Карты Microsoft Azure
description: Узнайте, как добавлять к картам многоугольники или круги. Сведения об использовании пакет SDK для Android Azure Maps для настройки геометрических фигур и упрощения их обновления и обслуживания.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1712cedab9cef23108fcc48b8e09bdc3e33065c4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679457"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Добавление слоя многоугольников на карту (пакет SDK для Android)

В этой статье показано, как визуализировать области геометрии компонентов `Polygon` и `MultiPolygon` на карте с помощью слоя многоугольников.

## <a name="prerequisites"></a>Предварительные требования

Не забудьте выполнить действия, описанные в разделе [Краткое руководство. Создание документа приложения Android](quick-android-map.md) . Блоки кода в этой статье можно вставить в `onReady` обработчик событий Maps.

## <a name="use-a-polygon-layer"></a>Использование слоя многоугольников

Если слой многоугольников подключен к источнику данных и загружен на карту, он отображает область с компонентами `Polygon` и `MultiPolygon`. Чтобы создать многоугольник, добавьте его в источник данных и выводите на слой многоугольников с помощью `PolygonLayer` класса.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

На следующем снимке экрана показан приведенный выше код для отрисовки области многоугольника с помощью слоя многоугольников.

![Многоугольник с отображаемой областью заполнения](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Использование многоугольников и слоев линий вместе

Контуры многоугольников можно визуализировать с помощью слоев линий. Следующий пример кода визуализирует многоугольник, как в предыдущем примере, но теперь добавляет слой линии. Этот слой линий является вторым слоем, подключенным к источнику данных.  

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

На следующем снимке экрана показан приведенный выше код, отображающий многоугольник с отображением его структуры с помощью слоя линий.

![Многоугольник с заполненной областью и структурой](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> При структурировании многоугольника с помощью линейного слоя следует закрывать все кольца в многоугольниках таким, чтобы каждый массив точек имеет одинаковую начальную и конечную точки. Если это не сделано, то слой линии не может соединять последнюю точку многоугольника с первой точкой.

## <a name="next-steps"></a>Следующие шаги

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Создание источника данных](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Добавление слоя линий](android-map-add-line-layer.md)
