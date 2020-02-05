---
title: Добавление слоя многоугольников в карту | Карты Microsoft Azure
description: В этой статье вы узнаете, как визуализировать многоугольник и несколько многоугольников на карте в веб-пакете SDK Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: bae47e2f5cd473893d97678977030643cc9949fe
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988623"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Добавление слоя многоугольников на карту

В этой статье показано, как визуализировать области `Polygon` и `MultiPolygon` геометрии компонентов на карте с помощью слоя многоугольников. Azure Maps веб-пакет SDK также поддерживает создание круговых геометрических объектов, как определено в [расширенной схеме геоjson](extend-geojson.md#circle). Эти круги преобразуются в многоугольники при отображении на карте. Все геометрические функции можно легко обновить при помощи [Atlas. Класс Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

## <a name="use-a-polygon-layer"></a>Использование слоя многоугольников 

Если слой многоугольников подключен к источнику данных и загружен на карту, он отображает область с `Polygon` и `MultiPolygon` функциями. Чтобы создать многоугольник, добавьте его в источник данных и выводите на слой многоугольников с помощью класса [полигонлайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) .

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

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

Ниже приведен полный и выполняемый пример приведенного выше кода.

<br/>

<iframe height='500' scrolling='no' title='Добавление многоугольника на карту ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Добавление многоугольника на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Совместное использование многоугольника и многострочного слоя

Слой линий используется для отрисовки контура многоугольников. Следующий пример кода визуализирует многоугольник, как в предыдущем примере, но теперь добавляет слой линии. Этот слой линии является вторым слоем, подключенным к источнику данных.  

<iframe height='500' scrolling='no' title='Слои многоугольников и линий для добавления многоугольника' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода для <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>добавления многоугольников с помощью слоев многоугольников и линий</a> от разработчиков Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Заливка многоугольника с помощью узора

Кроме заполнения многоугольника цветом, можно использовать узор изображения для заполнения многоугольника. Загрузите шаблон изображения в ресурсы с изображением Sprite, а затем сослаться на этот образ со свойством `fillPattern` слоя Polygon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Шаблон заливки многоугольника" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>шаблон заливка многоугольника</a> пера Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Веб-пакет SDK Azure Maps предоставляет несколько настраиваемых шаблонов изображений, которые можно использовать в качестве шаблонов заливки. Дополнительные сведения см. в документе [Использование шаблонов изображений](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-polygon-layer"></a>Настройка слоя многоугольников

Слой прямоугольников имеет несколько параметров стиля, которые можно опробовать с помощью представленного ниже средства.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpgL</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Добавление окружности к карте

Azure Maps использует расширенную версию схемы геоjson, которая предоставляет определение для кругов, как указано [здесь](extend-geojson.md#circle). Окружность отображается на карте путем создания `Point` функции. Этот `Point` имеет свойство `subType` со значением `"Circle"` и свойством `radius` с числом, представляющим радиус в метрах. 

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

Azure Maps веб-пакет SDK преобразует эти `Point` функции в функции `Polygon`. Затем эти функции отображаются на карте с использованием многоугольников и слоев линий, как показано в следующем примере кода.

<br/>

<iframe height='500' scrolling='no' title='Добавление круга на карту' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Добавление круга на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Упрощение обновления геометрии

`Shape` класс служит оболочкой для [геометрии](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) или [функции](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) и упрощает обновление и поддержку этих функций. Чтобы создать экземпляр переменной Shape, передайте геометрию или набор свойств в Конструктор Shape.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

В следующем образце кода показано, как обернуть объект-геоклассического объекта Circle с помощью класса Shape. При изменении значения радиуса в фигуре круг автоматически отображается на карте.

<br/>

<iframe height='500' scrolling='no' title='Обновление свойств фигуры' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>обновления свойств фигуры</a> от разработчиков Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Многоугольник](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [полигонлайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [полигонлайероптионс](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Создание источника данных](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавление всплывающего окна](map-add-popup.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Использование шаблонов изображений](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавить слой линий](map-add-line-layer.md)

Дополнительные ресурсы:

> [!div class="nextstepaction"]
> [Расширение спецификации геоjson Azure Maps](extend-geojson.md#circle)