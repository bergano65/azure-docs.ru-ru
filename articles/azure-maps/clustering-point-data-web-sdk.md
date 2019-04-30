---
title: Кластеризация точки данных в "карты Azure" | Документация Майкрософт
description: Кластеризация точки данных в Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d4dc6f0c8fd2dff74a1997c9dca5a31abc70c03a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795930"
---
# <a name="clustering-point-data"></a>Кластеризация точки данных

При визуализации большое число точек данных на карте, точек накладываться друг на друга, карты выглядит загроможденной и становится сложно видеть и использовать. Кластеризацию точки данных можно использовать для улучшения взаимодействия с пользователем. Кластеризация точки данных — это процесс объединения точки данных, которые находятся рядом друг с другом и представляя их на карте как одной кластеризованный точки данных. Как пользователь выполняет масштабирование в сопоставление, кластеры разбить на их отдельных точек данных.

## <a name="enabling-clustering-on-a-data-source"></a>Включение кластеризации на источнике данных

Кластеризация легко может включаться на `DataSource` класс, задав `cluster` значение true для параметра. Кроме того, radius пикселей для выбора близлежащих для объединения в кластер можно задать с помощью `clusterRadius` и значение масштаба может быть указан, по которому следует отключить кластеризации логики с помощью `clusterMaxZoom` параметр. Вот пример того, как включить кластеризацию в источнике данных.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximium zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> Если двух точек данных рядом друг с другом на землю, возможна никогда не нарушит друг от друга, независимо от того, насколько близко пользователя — увеличение кластера. Чтобы решить эту проблему, можно задать `clusterMaxZoom` параметр источника данных, который указывает на уровне масштаба для отключения кластеризации логики и все просто отображаться.

`DataSource` Класс также содержит следующие методы, относящиеся к кластеризации:

| Метод | Тип возвращаемого значения | Описание |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Обещание&lt;функция&lt;Geometry, любой&gt; \| фигуры&gt; | Получает дочерние узлы данного кластера, на следующий уровень масштаба. Эти дочерние объекты могут представлять собой сочетание фигур и subclusters. Subclusters будет функции, свойства которых соответствуют ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Обещание&lt;номер&gt; | Вычисляет значение масштаба, по которому запускается расширение или разбить на части кластера. |
| getClusterLeaves (clusterId: number, ограничение: число, смещения: номер) | Обещание&lt;функция&lt;Geometry, любой&gt; \| фигуры&gt; | Извлекает все точки в кластере. Задайте `limit` возврата подмножества точки, и использовать `offset` страницу через точки. |

## <a name="display-clusters-using-a-bubble-layer"></a>Отображать кластеры с помощью слоя пузырьков

Пузырьковая слой является отличным способом для подготовки к просмотру кластеризованного точек, как можно легко масштабировать радиус и их зависимости от числа точек в кластере с помощью выражения цвета. При отображении кластеров с помощью слоя пузырьков, также следует использовать отдельный слой для подготовки к просмотру вне кластера доступ к данным. Часто бывает желательно также сможет отображать размер кластера на основе пузырьков. Слой символ с текстом и значком не может использоваться для достижения этого поведения. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Слой простой пузырьковой кластеризации" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>кластеризации слоя простой пузырьковой</a> по "карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Отображать кластеры с помощью слоя символ

При визуализации данных точки, используя уровень символа, по умолчанию, он будет автоматически скрывать символы перекрывающиеся друг с другом для обеспечения работы в чище, тем не менее это может быть требуемой работы, если вы хотите увидеть плотность данных указывает на карте. Установка `allowOverlap` параметр слоев символ `iconOptions` свойства `true` отключает этот опыт, но приведет к всех отображаемых символов. С помощью кластеризации позволяет плотность всех данных см. в разделе привлекательным пользовательским чистой удобную работу. В этом образце пользовательского символов будет использоваться для представления кластеров и отдельные точки данных.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Уровень кластеризованного символ" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>уровень кластеризованных символ</a> по "карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Кластеризация и тепло сопоставляет слоя

Карты рисков являются отличным способом для отображения плотность данных на карте. Эта визуализация может обрабатывать большое количество точек данных сама по себе, но он может обрабатывать еще больше данных, если точки данных расположены компактными и размера кластера используется в качестве веса тепловой карты. Задайте `weight` параметр тепловой карты слоя, чтобы `['get', 'point_count']` для достижения этой цели. При небольшом radius кластера тепловая карта будет выглядеть почти идентична тепловую карту с помощью точек данных вне кластера, но намного эффективнее использовать. Тем не менее чем меньше кластера радиус, тем точнее тепловой карты будут, но меньше производительности, пользоваться преимуществами.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Кластер со смещением тепловая карта" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>кластера со смещением тепловая карта</a> по "карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>События мыши в точках кластеризованных данных

При возникновении событий мыши в слое, содержать кластеризованный доступ к данным, точки кластеризованных данных будет возвращаться к событию как объект функции точки GeoJSON. Эта функция точка будет иметь следующие свойства:

| Имя свойства | type | Описание |
|---------------|------|-------------|
| cluster | Логическое | Указывает, представляет ли функция кластера. |
| cluster_id | string | Уникальный идентификатор для кластера, который может использоваться с источником данных `getClusterExpansionZoom`, `getClusterChildren`, и `getClusterLeaves` методы. |
| point_count | номер | Количество точек, которые содержит кластер. |
| point_count_abbreviated | string | Строка, которая сокращает point_count значение, если он имеет большую длину. (например, 4000 становится 4K) |

В этом примере принимает слоя пузырьков, который отображает точки кластера и добавит событие щелчка, при активации, вычисления и масштабировать карту на новый уровень масштабирования, по которому кластер приведет к разрыву друг от друга с помощью `getClusterExpansionZoom` метод `DataSource` класс и `cluster_id` нажатого свойство clustered точки данных. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom кластера" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom кластера</a> по "карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Область отображения кластера 

Точки данных, представляющий кластер распределяется по области. В этом примере, при наведении курсора мыши на кластере отдельных точек, что он содержит (листья) будут используется для расчета выпуклой оболочки и отображаться на карте, чтобы показать область данных. Все точки, содержащихся в кластере могут быть получены из источника данных с помощью `getClusterLeaves` метод. Выпуклая оболочка — многоугольник, который создает оболочку для набора точек, elastic аппаратного контроллера управления, такие как и можно вычислить с помощью `atlas.math.getConvexHull` метод.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Выпуклая оболочка области кластера" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>кластера области выпуклая оболочка</a> по "карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Класс источника данных](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Объект DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math namespace](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

См. примеры кодов для добавления функций в приложение:

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя символов](map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление слоя карты рисков](map-add-heat-map-layer.md)
