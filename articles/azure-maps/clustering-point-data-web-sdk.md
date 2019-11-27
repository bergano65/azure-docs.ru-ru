---
title: Данные точки кластеризации в Azure Maps | Документация Майкрософт
description: Как кластерные точки данных в веб-пакете SDK
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 4a583f77aac036028fd75d3c05af805031f08ebd
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480551"
---
# <a name="clustering-point-data"></a>Данные точки кластеризации

При визуализации большого количества точек данных на карте точки перекрываются, а схема выглядит недостаточной и ее можно будет увидеть и использовать. Кластеризацию точечных данных можно использовать для улучшения взаимодействия с пользователем. Данные точки кластеризации — это процесс объединения данных точек, расположенных рядом друг с другом и представляющих их на карте как единую кластеризованную точку данных. По мере того, как пользователь масштабирует карту, кластеры разбиваются на отдельные точки данных.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Включение кластеризации в источнике данных

Кластеризацию можно легко включить на `DataSource` классе, задав для параметра `cluster` значение true. Кроме того, радиус пикселя для выбора ближайших точек, объединяемых в кластер, можно задать с помощью `clusterRadius` а уровень масштабирования можно указать, чтобы отключить логику кластеризации с помощью параметра `clusterMaxZoom`. Ниже приведен пример включения кластеризации в источнике данных.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Если две точки данных близки друг к другу, возможно, кластер никогда не будет разбиваться, независимо от того, насколько близки к масштабу пользователя. Для решения этой задачи можно задать параметр `clusterMaxZoom` источника данных, который указывает на уровне масштабирования, чтобы отключить логику кластеризации и просто отобразить все данные.

Класс `DataSource` также содержит следующие методы, связанные с кластеризацией:

| Метод | Тип возвращаемого значения | ОПИСАНИЕ |
|--------|-------------|-------------|
| Жетклустерчилдрен (clusterId: число) | Promise&lt;Array&lt;функция&lt;Geometry, любая&gt; \| форма&gt;&gt; | Извлекает дочерние элементы заданного кластера на следующем уровне масштабирования. Эти дочерние элементы могут быть сочетанием фигур и подкластеров. Подкластеры будут содержать функции со свойствами, соответствующими Клустередпропертиес. |
| Жетклустерекспансионзум (clusterId: число) | Номер&lt;Promise&gt; | Вычисляет уровень масштабирования, при котором кластер начинает разворачиваться или разбиваться на части. |
| Жетклустерлеавес (clusterId: число, ограничение: число, смещение: число) | Promise&lt;Array&lt;функция&lt;Geometry, любая&gt; \| форма&gt;&gt; | Извлекает все точки в кластере. Задайте `limit`, чтобы вернуть подмножество точек, и используйте `offset`, чтобы пролистать точки. |

## <a name="display-clusters-using-a-bubble-layer"></a>Отображение кластеров с помощью пузырькового слоя

Пузырьковый слой — это отличный способ отображения кластеризованных точек, так как можно легко масштабировать радиус и изменять цвет на основе числа точек в кластере с помощью выражения. При отображении кластеров с помощью пузырькового слоя следует также использовать отдельный слой для визуализации точек данных, которые не являются кластеризованными. Зачастую удобно также иметь возможность отображать размер кластера поверх пузырьков. Для достижения этого поведения можно использовать слой символов с текстом и без значка. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Базовый уровень кластеризации пузырьков" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Дополнительные сведения см. в разделе <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>кластеризация уровня "базовый</a> " с помощью Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Отображение кластеров с помощью слоя символов

При визуализации данных точки с помощью слоя символов по умолчанию автоматически скрываются символы, которые пересекаются друг с другом, для создания процесса очистки, однако это может оказаться нежелательным, если вы хотите увидеть плотность точек данных на карте. Установка параметра `allowOverlap` для свойства "слои символов" `iconOptions` свойство в значение `true` отключает этот интерфейс, но приводит к отображению всех отображаемых символов. Использование кластеризации позволяет видеть плотность всех данных при создании удобного четкого интерфейса пользователя. В этом примере для представления кластеров и отдельных точек данных будут использоваться пользовательские символы.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Кластеризованный слой символов" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Сведения о том, как Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>, см. в разделе <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>уровень кластеризованных символов</a> пера.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Кластеризация и уровень тепловых карт

Тепловые карты — это отличный способ отображения плотности данных на карте. Эта визуализация может самостоятельно справляться с большим количеством точек данных, но она может управлять еще большим объемом данных, если точки данных кластеризованы, а размер кластера используется в качестве веса тепловой карт. Чтобы добиться этого, задайте для параметра `weight` слоя тепловой карт значение `['get', 'point_count']`. Если радиус кластера невелик, тепловая схема будет выглядеть почти так же, как тепловая, с использованием некластеризованных точек данных, но будет работать гораздо лучше. Тем не менее, чем меньше радиус кластера, тем точнее будет тепловая схема, но с меньшим преимуществом производительности.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Взвешенная тепловая схема кластера" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>взвешенная тепловая текстура кластера</a> с Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>События мыши на точках кластеризованных данных

При возникновении событий мыши на слое, содержащем кластеризованные точки данных, кластеризованная точка данных будет возвращена в событие в качестве объекта функции геообъектной точки. Этот компонент будет иметь следующие свойства:

| Имя свойства | введите | ОПИСАНИЕ |
|---------------|------|-------------|
| cluster | логическое значение | Указывает, представляет ли компонент кластер. |
| cluster_id | строка | Уникальный идентификатор кластера, который можно использовать с методами `getClusterExpansionZoom`DataSource, `getClusterChildren`и `getClusterLeaves`. |
| point_count | number | Число точек, содержащихся в кластере. |
| point_count_abbreviated | строка | Строка, которая сокращает значение `point_count`, если оно длинное. (например, 4 000 преобразуется в 4 КБ) |

В этом примере используется пузырьковый слой, который готовит к просмотру точки кластера и добавляет событие щелчка, которое при активации, вычисляются и масштабирует карту до следующего уровня масштаба, на котором кластер будет разбиваться с помощью метода `getClusterExpansionZoom` класса `DataSource` и свойства `cluster_id` в кластеризованной точке данных, которую щелкнули. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Жетклустерекспансионзум кластера" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Жетклустерекспансионзум Clustering</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Отобразить область кластера 

Данные точки, которые представляет кластер, распределяются по области. В этом примере при наведении указателя мыши на кластер, отдельные точки данных, которые он содержит (листья), будут использоваться для вычисления выпуклой поверхности и отображения на карте для отображения области. Все точки, содержащиеся в кластере, можно получить из источника данных с помощью метода `getClusterLeaves`. Выпуклой поверхности — это многоугольник, который заключает набор точек, например эластичную полосу, и может быть вычислен с помощью метода `atlas.math.getConvexHull`.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Выпуклой поверхности области кластера" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>кластерная область выпуклой поверхности</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Статистическая обработка данных в кластерах

Часто кластеры представляются с помощью символа с количеством точек в кластере, однако иногда желательно настроить стиль кластеров на основе некоторой метрики, например общего дохода всех точек в кластере. С помощью статистических выражений Cluster можно создавать и заполнять пользовательские свойства, используя вычисление [статистического выражения](data-driven-style-expressions-web-sdk.md#aggregate-expression) .  Агрегаты кластера можно определить в `clusterProperties` параметре `DataSource`.

В следующем примере используется статистическое выражение для вычисления количества на основе свойства типа сущности каждой точки данных в кластере.

<iframe height="500" style="width: 100%;" scrolling="no" title="Статистические выражения кластера" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>агрегаты кластера</a> перьев по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Класс DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Объект DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [пространство имен Atlas. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

См. примеры кодов для добавления функций в приложение:

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя символов](map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление слоя тепловой карт](map-add-heat-map-layer.md)
