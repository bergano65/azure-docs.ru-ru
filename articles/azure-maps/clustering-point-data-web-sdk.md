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
ms.openlocfilehash: 5f51c1166364a3470a1cc943e66d429c32cdc49b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839476"
---
# <a name="clustering-point-data"></a>Данные точки кластеризации

При визуализации большого количества точек данных на карте точки перекрываются, а схема выглядит недостаточной и ее можно будет увидеть и использовать. Кластеризацию точечных данных можно использовать для улучшения взаимодействия с пользователем. Данные точки кластеризации — это процесс объединения данных точек, расположенных рядом друг с другом и представляющих их на карте как единую кластеризованную точку данных. По мере того, как пользователь масштабирует карту, кластеры разбиваются на отдельные точки данных.

## <a name="enabling-clustering-on-a-data-source"></a>Включение кластеризации в источнике данных

Кластеризацию можно легко включить в `DataSource` классе, `cluster` задав для параметра значение true. Кроме того, радиус пикселя для выбора ближайших точек, объединяемых в кластер, можно задать `clusterRadius` с помощью, а уровень масштабирования можно указать, чтобы отключить логику кластеризации `clusterMaxZoom` с помощью параметра. Ниже приведен пример включения кластеризации в источнике данных.

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
> Если две точки данных близки друг к другу, возможно, кластер никогда не будет разбиваться, независимо от того, насколько близки к масштабу пользователя. Для решения этой задачи можно задать `clusterMaxZoom` параметр источника данных, который задает уровень масштабирования, чтобы отключить логику кластеризации и просто отобразить все данные.

`DataSource` Класс также содержит следующие методы, связанные с кластеризацией:

| Метод | Тип возвращаемого значения | Описание |
|--------|-------------|-------------|
| Жетклустерчилдрен (clusterId: число) | Геометрическая&lt;функция&lt;&lt;массива Promise&gt; , любая \| фигура&gt;&gt; | Извлекает дочерние элементы заданного кластера на следующем уровне масштабирования. Эти дочерние элементы могут быть сочетанием фигур и подкластеров. Подкластеры будут содержать функции со свойствами, соответствующими Клустередпропертиес. |
| Жетклустерекспансионзум (clusterId: число) | Номер&lt;Promise&gt; | Вычисляет уровень масштабирования, при котором кластер начинает разворачиваться или разбиваться на части. |
| Жетклустерлеавес (clusterId: число, ограничение: число, смещение: число) | Геометрическая&lt;функция&lt;&lt;массива Promise&gt; , любая \| фигура&gt;&gt; | Извлекает все точки в кластере. Задайте для значение, чтобы вернуть подмножество точек, и `offset` используйте для просмотра точек. `limit` |

## <a name="display-clusters-using-a-bubble-layer"></a>Отображение кластеров с помощью пузырькового слоя

Пузырьковый слой — это отличный способ отображения кластеризованных точек, так как можно легко масштабировать радиус и изменять цвет на основе числа точек в кластере с помощью выражения. При отображении кластеров с помощью пузырькового слоя следует также использовать отдельный слой для визуализации точек данных, которые не являются кластеризованными. Зачастую удобно также иметь возможность отображать размер кластера поверх пузырьков. Для достижения этого поведения можно использовать слой символов с текстом и без значка. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Базовый уровень кластеризации пузырьков" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел Создание высокоуровневой пузырьковой <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>категории</a> с помощью<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Отображение кластеров с помощью слоя символов

При визуализации данных точки с помощью слоя символов по умолчанию автоматически скрываются символы, которые пересекаются друг с другом, для создания процесса очистки, однако это может оказаться нежелательным, если вы хотите увидеть плотность точек данных на карте. Установка параметра для свойства "слои `iconOptions` символов" отключает `true` этот интерфейс, но приводит к отображению всех отображаемых символов. `allowOverlap` Использование кластеризации позволяет видеть плотность всех данных при создании удобного четкого интерфейса пользователя. В этом примере для представления кластеров и отдельных точек данных будут использоваться пользовательские символы.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Кластеризованный слой символов" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. Azure Maps <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'></a> () на<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>().
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Кластеризация и уровень тепловых карт

Тепловые карты — это отличный способ отображения плотности данных на карте. Эта визуализация может самостоятельно справляться с большим количеством точек данных, но она может управлять еще большим объемом данных, если точки данных кластеризованы, а размер кластера используется в качестве веса тепловой карт. `weight` Установите параметр `['get', 'point_count']` слоя тепловой карт, чтобы добиться этого. Если радиус кластера невелик, тепловая схема будет выглядеть почти так же, как тепловая, с использованием некластеризованных точек данных, но будет работать гораздо лучше. Тем не менее, чем меньше радиус кластера, тем точнее будет тепловая схема, но с меньшим преимуществом производительности.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Взвешенная тепловая схема кластера" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Ознакомьтесь с <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>взвешенной тепловой картой кластера</a> с помощью Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>События мыши на точках кластеризованных данных

При возникновении событий мыши на слое, содержащем кластеризованные точки данных, кластеризованная точка данных будет возвращена в событие в качестве объекта функции геообъектной точки. Этот компонент будет иметь следующие свойства:

| Имя свойства | Тип | Описание |
|---------------|------|-------------|
| кластер | Логический | Указывает, представляет ли компонент кластер. |
| cluster_id | строка | Уникальный идентификатор кластера, который можно использовать с методами DataSource `getClusterExpansionZoom`, `getClusterChildren`и `getClusterLeaves` . |
| point_count | номер | Число точек, содержащихся в кластере. |
| point_count_abbreviated | строка | Строка, которая сокращает `point_count` значение, если оно длинное. (например, 4 000 преобразуется в 4 КБ) |

В этом примере используется пузырьковый слой, который готовит к просмотру точки кластера и добавляет событие щелчка, которое при активации, вычисляются и масштабирует карту до следующего уровня масштаба, на котором кластер будет разбиваться с `getClusterExpansionZoom` помощью `DataSource` метода класса и `cluster_id` для кластеризованной точки данных, которую щелкнули. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Жетклустерекспансионзум кластера" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/moZWeV/'>жетклустерекспансионзум Clustering</a> by Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() ON <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Отобразить область кластера 

Данные точки, которые представляет кластер, распределяются по области. В этом примере при наведении указателя мыши на кластер, отдельные точки данных, которые он содержит (листья), будут использоваться для вычисления выпуклой поверхности и отображения на карте для отображения области. Все точки, содержащиеся в кластере, можно получить из источника данных с `getClusterLeaves` помощью метода. Выпуклой поверхности — это многоугольник, который заключает набор точек, например эластичную полосу, и может быть вычислен с помощью `atlas.math.getConvexHull` метода.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Выпуклой поверхности области кластера" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>кластерное перо выпуклой поверхности</a> by Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Статистическая обработка данных в кластерах

Часто кластеры представляются с помощью символа с количеством точек в кластере, однако иногда желательно настроить стиль кластеров на основе некоторой метрики, например общего дохода всех точек в кластере. С помощью статистических выражений Cluster можно создавать и заполнять пользовательские свойства, используя вычисление [статистического выражения](data-driven-style-expressions-web-sdk.md#aggregate-expression) .  Статистические выражения кластера можно определить в `clusterProperties` параметре `DataSource`.

В следующем примере используется статистическое выражение для вычисления количества на основе свойства типа сущности каждой точки данных в кластере.

<iframe height="500" style="width: 100%;" scrolling="no" title="Статистические выражения кластера" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>агрегаты кластера</a> перьев по Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Следующие шаги

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
