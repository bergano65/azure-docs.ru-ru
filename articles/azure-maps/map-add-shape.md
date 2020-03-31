---
title: Добавьте слой полигона на карту Карты Microsoft Azure
description: В этой статье вы узнаете, как сделать полигон и мультиполигон на карте в Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535058"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Добавьте на карту слой полигона

В этой статье показано, как `Polygon` `MultiPolygon` отобразить области и объекты геометрии на карте с помощью полигонного слоя. Веб-SDK Azure Maps также поддерживает создание геометрий Круга, как это определено в [расширенной схеме GeoJSON.](extend-geojson.md#circle) Эти круги трансформируются в полигоны при отображении на карте. Все функции геометрии могут быть легко обновлены при завернутый с [атласом. Класс формы.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-layer"></a>Используйте слой полигона 

Когда слой полигона подключен к источнику данных и загружается на `Polygon` `MultiPolygon` карту, он отображает область с и особенностей. Чтобы создать полигон, добавьте его в источник данных и свизуруйте с помощью многогонных слоев с помощью класса [PolygonLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Ниже приведен полный и запущенный образец вышеуказанного кода.

<br/>

<iframe height='500' scrolling='no' title='Добавление многоугольника на карту ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Добавление многоугольника на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Используйте полигон и линейный слой вместе

Для визуализации контуров полигонов используется линейный слой. Следующий пример кода отображает полигон, как предыдущий пример, но теперь добавляет слой строки. Этот линейный слой представляет собой второй слой, подключенный к источнику данных.  

<iframe height='500' scrolling='no' title='Слои многоугольников и линий для добавления многоугольника' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода для <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>добавления многоугольников с помощью слоев многоугольников и линий</a> от разработчиков Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Заполните полигон узором

В дополнение к заполнению полигона с цветом, вы можете использовать шаблон изображения для заполнения полигона. Загрузите рисунок изображения в ресурсы спрайта `fillPattern` изображения карт, а затем отображение этого изображения с свойством полигонного слоя.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Шаблон заполнения полигона" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите шаблон заполнения Pen <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon</a> по<a href='https://codepen.io/azuremaps'>@azuremaps</a>Картам Azure () на <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Веб-SDK Azure Maps предоставляет несколько настраиваемых шаблонов изображений, которые можно использовать в качестве шаблонов заполнения. Для получения дополнительной информации смотрите документ [«Как использовать шаблоны изображений».](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-polygon-layer"></a>Настройка слоя многоугольников

Слой прямоугольников имеет несколько параметров стиля, которые можно опробовать с помощью представленного ниже средства.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpgL</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Добавление окружности на карту

Azure Maps использует расширенную версию схемы GeoJSON, которая содержит определение для кругов, как отмечено [здесь.](extend-geojson.md#circle) Круг отображается на карте путем `Point` создания функции. Это `Point` свойство `subType` со значением `"Circle"` и свойство `radius` с номером, который представляет радиус в метрах. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Веб-SDK Azure Maps `Point` преобразует `Polygon` эти функции в функции. Затем эти функции отображаются на карте с использованием многоугольных и линейных слоев, как показано в следующем образце кода.

<br/>

<iframe height='500' scrolling='no' title='Добавление круга на карту' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Добавление круга на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Упростите обновление геометрии

Класс `Shape` обертывает [геометрию](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) или [функцию](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) и упрощает обновление и обслуживание этих функций. Чтобы мгновенно изменить форму, передайте геометрию или набор свойств конструктору формы.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Следующий пример кода показывает, как обернуть круг объекта GeoJSON с классом формы. По мере изменения значения радиуса в форме круг автоматически отображается на карте.

<br/>

<iframe height='500' scrolling='no' title='Обновление свойств фигуры' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>обновления свойств фигуры</a> от разработчиков Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Полигона](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ПолигонСлой](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ПолигонLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

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
> [Добавление слоя линий](map-add-line-layer.md)

Дополнительные ресурсы:

> [!div class="nextstepaction"]
> [Расширение спецификации Azure Maps GeoJSON](extend-geojson.md#circle)