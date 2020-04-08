---
title: Данные о точках кластеризации на карте Карты Microsoft Azure
description: В этой статье вы узнаете, как кластерить точечные данные и визуализировать их на карте с помощью веб-SDK Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: ce2891201331ee1efd861d2f13cec78c0551b6ba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804577"
---
# <a name="clustering-point-data"></a>Данные точек кластеризации

При визуализации многих точек данных на карте точки данных могут перекрываться друг с другом. Перекрытие может привести к тому, что карта может стать нечитаемой и трудной в использовании. Кластеризация данных точек — это процесс объединения данных точек, находящихся рядом друг с другом, и представления их на карте в виде единой кластеризованной точки данных. По мере того как пользователь масштабирует карту, кластеры разбиваются на отдельные точки данных. Работая с большим количеством точек данных, используйте процессы кластеризации для улучшения работы пользователей.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Включение кластеризации источника данных

Включить кластеризирование `DataSource` в классе, установив опцию `cluster` на истину. Установите `ClusterRadius` для выбора близлежащих точек и объединяет их в кластер. Значение `ClusterRadius` находится в пикселях. Используйте `clusterMaxZoom` для уточнения уровня масштабирования, на котором можно отключить логику кластеризации. Вот пример того, как включить кластеризм в источнике данных.

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
> Если две точки данных находятся близко друг к другу на земле, возможно, кластер никогда не расстанется, независимо от того, насколько близко пользователь приближается. Для решения этой проблемы `clusterMaxZoom` можно настроить опцию отключить логику кластеризации и просто отобразить все.

Вот дополнительные методы, которые `DataSource` класс предоставляет для кластеризации:

| Метод | Возвращаемый тип | Описание |
|--------|-------------|-------------|
| getClusterChildren (clusterId: номер) | &lt;Выполнить&lt;&lt;Array Функция&gt; \| геометрии, любая форма&gt;&gt; | Извлекает дочерние элементы заданного кластера на следующем уровне масштабирования. Эти дочерние элементы могут быть сочетанием фигур и подкластеров. Подкластеры будут представлять собой компоненты со свойствами, соответствующими ClusteredProperties. |
| getClusterРасширениеЗоом (кластерИД: номер) | Promise&lt;число&gt; | Вычисляет уровень масштабирования, при котором кластер начинает расширяться или разбиваться на части. |
| getClusterLeaves (clusterId: число, лимит: число, смещение: число) | &lt;Выполнить&lt;&lt;Array Функция&gt; \| геометрии, любая форма&gt;&gt; | Извлекает все точки в кластере. Задайте `limit`, чтобы вернуть подмножество точек, и используйте `offset`, чтобы пролистать страницы с точками. |

## <a name="display-clusters-using-a-bubble-layer"></a>Отображение кластеров с помощью слоя пузыря

Слой пузыря — это отличный способ визуализации кластерных точек. Используйте выражения для масштабирования радиуса и изменения цвета в зависимости от количества точек в кластере. Если вы отображаете кластеры с помощью слоя пузыря, то следует использовать отдельный слой для визуализации некластеризированных точек данных.

Чтобы отобразить размер кластера поверх пузыря, используйте слой символа с текстом и не используйте значок.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Кластеризация базового слоя пузыря" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>кластеризации слоя пузыря</a> Pen Basic<a href='https://codepen.io/azuremaps'>@azuremaps</a>по картам Azure () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Отображение кластеров с помощью слоя символа

При визуализации точек данных слой символа автоматически скрывает символы, которые перекрывают друг друга, чтобы обеспечить более чистый пользовательский интерфейс. Это поведение по умолчанию может быть нежелательным, если вы хотите показать плотность точек данных на карте. Однако эти параметры могут быть изменены. Чтобы отобразить `allowOverlap` все символы, `iconOptions` установите опцию свойства слоев символа. `true` 

Используйте кластеризм, чтобы показать плотность точек данных, сохраняя при этом чистый пользовательский интерфейс. Приведенный ниже пример показывает, как добавлять пользовательские символы и представлять кластеры и отдельные точки данных с помощью слоя символа.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Слой кластерного символа" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>слой символа с кластером</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pen по картам Azure () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Кластеризация и слой тепловых карт

Тепловые карты являются отличным способом для отображения плотности данных на карте. Этот метод визуализации может обрабатывать большое количество точек данных самостоятельно. Если точки данных группируются и размер кластера используется в качестве веса тепловой карты, то карта тепла может обрабатывать еще больше данных. Для достижения этой опции установите `weight` опцию `['get', 'point_count']`слоя тепловой карты. Когда радиус скопления небольшой, тепловая карта будет выглядеть почти идентичной тепловой карте с использованием некластерных точек данных, но она будет работать гораздо лучше. Тем не менее, чем меньше радиус кластера, тем точнее будет тепловая карта, но с меньшими преимуществами производительности.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Кластер взвешенная карта тепла" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>перо кластера взвешенных ТеплоКарта</a> Azure Maps ( )<a href='https://codepen.io/azuremaps'>@azuremaps</a>на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>События мыши на кластерных точках данных

Когда события мыши происходят на слое, содержащем кластерные точки данных, кластерная точка данных возвращается к событию в качестве объекта элемента геоэлемента. Эта точечная функция будет иметь следующие свойства:

| Имя свойства             | Тип    | Описание   |
|---------------------------|---------|---------------|
| `cluster`                 | Логическое | Указывает, представляет ли компонент кластер. |
| `cluster_id`              | строка  | Уникальный идентификатор кластера, который можно использовать с методами `getClusterExpansionZoom`, `getClusterChildren` и `getClusterLeaves` для DataSource. |
| `point_count`             | number  | Число точек, содержащихся в кластере.  |
| `point_count_abbreviated` | строка  | Строка, которая сокращает `point_count` значение, если оно длинное. (например, 4000 преобразуется в 4K).  |

В этом примере приводится слой пузыря, который отображает точки кластера и добавляет событие щелчка. При запуске события щелчка код вычисляет и масштабирует карту до следующего уровня масштабирования, на котором кластер распадается. Эта функциональность реализована `getClusterExpansionZoom` с `DataSource` использованием `cluster_id` метода класса и свойства нажатой точки кластеризации данных.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Кластер получитьКластерРасширение Зума" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Перский кластер getClusterРасширения По</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Область кластера отображения 

Точечные данные, которые представляет кластер, распределены по области. В этом примере, когда мышь зависает над кластером, происходят два основных поведения. Во-первых, отдельные точки данных, содержащиеся в кластере, будут использоваться для расчета выпуклого корпуса. Затем выпуклость корпуса будет отображаться на карте, чтобы показать область.  Выпуклый корпус представляет собой полигон, который обертывает набор точек, `atlas.math.getConvexHull` как резинка и может быть рассчитанс с помощью метода. Все точки, содержащиеся в кластере, могут `getClusterLeaves` быть извлечены из источника данных с помощью метода.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Кластерная область выпуклого корпуса" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>выпуклую область пен-кластера</a> по Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Агрегирование данных в кластерах

Часто кластеры представлены с помощью символа с количеством точек, которые находятся в кластере. Но иногда желательно настроить стиль кластеров с дополнительными метриками. С помощью кластерных агрегатов пользовательские свойства могут создаваться и заполняться с помощью [расчета агрегированных выражений.](data-driven-style-expressions-web-sdk.md#aggregate-expression)  Кластерные агрегаты могут `clusterProperties` быть `DataSource`определены в варианте .

В следующем примере используется агрегированное выражение. Код вычисляет подсчет на основе свойства типа сущности каждой точки данных в кластере. Когда пользователь нажимает на кластер, всплывающее окно отображает дополнительную информацию о кластере.

<iframe height="500" style="width: 100%;" scrolling="no" title="Кластерные агрегаты" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>агрегаты кластера</a> пера<a href='https://codepen.io/azuremaps'>@azuremaps</a>по Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [DataSource class](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Объект DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math namespace](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

См. примеры кодов для добавления функций в приложение:

> [!div class="nextstepaction"]
> [Добавить слой пузыря](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя символов](map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление слоя тепловой карты](map-add-heat-map-layer.md)
