---
title: Добавление фигуры с помощью службы "Карты Azure" | Документация Майкрософт
description: Как добавить фигуру на карту в JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1e550002948fc1320b8645bf1af635536d524fe6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282395"
---
# <a name="add-a-shape-to-a-map"></a>Добавление фигуры на карту

В этой статье показано, как для подготовки к просмотру геометрических объектов на карте с помощью строки, а также многоугольников слоев. SDK веб-карты Azure также поддерживает создание геометрических объектов круг, как определено в [расширенная схема GeoJSON](extend-geojson.md#circle). Все функции геометрические объекты можно также легко обновить, если в оболочку с [фигуры](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) класса.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Добавьте строки на карту

`LineString` и `MultiLineString` функции используются для представления путей, а также описывается на карте.

## <a name="use-a-line"></a>С помощью строки кода

<iframe height='500' scrolling='no' title='Добавление линии на карту' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Добавление линии на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Объект [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) создается и добавляется к источнику данных.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) отвечает за отображение линейных объектов в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Последний блок кода позволят создать и добавить на карту слой линий. Ознакомьтесь со свойствами слоя линий в разделе о классе [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Создаются и добавляются на карту в источнике данных и слой линий [обработчик событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) чтобы убедиться, что строка отображается после полной загрузки карты.

### <a name="add-symbols-along-a-line"></a>Добавьте символы вдоль линии

В этом примере показано, как добавлять значки-стрелки вдоль линии на карте. Если с помощью слоя символ, значение «размещение» возможность «строка», будет отображать символы вдоль линии и поворот значки (0 градусов = right).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Показать стрелку вдоль линии" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Показать стрелку вдоль линии</a> по "карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a> Добавление градиента stroke строки

Кроме того, что возможность применить цвет одним росчерком к строке можно ввести строку с градиентные заливки, осуществить переход из одного сегмента линии к следующему. Например градиенты строки можно использовать для представления изменения по времени и расстояния или другой температуры в строку связанных объектов. Чтобы применить эту функцию со строкой, источник данных должен иметь `lineMetrics` параметра задано значение true, а затем может передаваться выражении градиента цвета в `strokeColor` параметр строки. Выражение градиента stroke приходится ссылку `['line-progress']` данных выражение, которое предоставляет метрики вычисляемый строки в выражение.

<br/>

<iframe height="265" style="width: 100%;" scrolling="no" title="Линия с градиентом штриха" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=265&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>линия с градиентом Stroke</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Настройка слоя линий

Слой линий имеет несколько параметров стиля, которые можно опробовать с помощью определенного средства.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя линий' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода с <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>параметрами слоя линий</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Добавить многоугольника на карту

`Polygon` и `MultiPolygon` функции часто используются для представления области на карте. 

### <a name="use-a-polygon-layer"></a>Использовать слой многоугольников 

Слой многоугольников отображает область многоугольника. 

<iframe height='500' scrolling='no' title='Добавление многоугольника на карту ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Добавление многоугольника на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). [Многоугольник](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) создается на основе массива координат и добавляется к источнику данных. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) отвечает за отображение данных, заключенных в [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), на карте. Последний блок кода позволяет создать и добавить на карту слой многоугольников. Просмотрите свойства слоя многоугольников в разделе о классе [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Создаются и добавляются на карту в источнике данных, а слой многоугольников [обработчик событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) чтобы убедиться, что многоугольник отображается после полной загрузки карты.

### <a name="use-a-polygon-and-line-layer-together"></a>Совместно использовать слой многоугольников и линий

Слой линий можно использовать для отображения структуры многоугольника. 

<iframe height='500' scrolling='no' title='Слои многоугольников и линий для добавления многоугольника' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода для <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>добавления многоугольников с помощью слоев многоугольников и линий</a> от разработчиков Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). [Многоугольник](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) создается на основе массива координат и добавляется к источнику данных. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) отвечает за отображение данных, заключенных в [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), на карте. Просмотрите свойства слоя многоугольников в разделе о классе [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) представляет собой массив линий. Ознакомьтесь со свойствами слоя линий в разделе о классе [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Третий блок кода позволяет создать слои многоугольников и линий.

В последнем блоке кода слои многоугольников и линий добавляются на карту. Создаются и добавляются на карту в источнике данных и уровни [обработчик событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) чтобы убедиться, что многоугольник отображается после полной загрузки карты.

> [!TIP]
> Слои строки по умолчанию подготавливается к просмотру координат многоугольников, а также строки в источнике данных. Чтобы ограничить на уровне, таким образом, чтобы он отображает только LineString компоненты набора `filter` свойства слоя, чтобы `['==', ['geometry-type'], 'LineString']` или `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` Если вы хотите включить функции MultiLineString также.

### <a name="fill-a-polygon-with-a-pattern"></a>Заполнение многоугольника с помощью шаблона

Кроме заполнения многоугольник цветом можно использоваться шаблон образа. Загрузить шаблон изображения в ресурсы спрайт изображения карты и затем ссылаться на этот образ с `fillPattern` свойства слоя многоугольников.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Шаблон многоугольника fill" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>шаблона многоугольника fill</a> по "карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Настройка слоя многоугольников

Слой прямоугольников имеет несколько параметров стиля, которые можно опробовать с помощью определенного средства.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpgL</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Добавить круг на карту

Azure Maps использует расширенную версию схемы GeoJSON, которая предоставляет определения для кругов, как было отмечено [здесь](extend-geojson.md#circle). Окружность может визуализироваться на карте, создав `Point` функцию с `subType` свойство со значением `"Circle"` и `radius` свойство, которое содержит число, представляющее радиус в метрах. Например: 

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

SDK веб-карты Azure преобразует эти `Pooint` функции в `Polygon` функций на самом деле and может передаваться на карте с помощью слоев многоугольников и линий, как показано ниже.

<iframe height='500' scrolling='no' title='Добавление круга на карту' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Добавление круга на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). — Круг [функция](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) из [точки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) и имеет `subType` свойство значение `"Circle"` и `radius` значение свойства в метрах. Когда компонент точки с `subType` из `"Circle"` добавляется к источнику данных, оно преобразуется в циклических многоугольника в схеме.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) отвечает за отображение данных, заключенных в [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), на карте. Последний блок кода позволяет создать и добавить на карту слой многоугольников. Просмотрите свойства слоя многоугольников в разделе о классе [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Создаются и добавляются на карту в источнике данных, а слой многоугольников [обработчик событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) чтобы убедиться, что элемент управления circle отображается после полной загрузки карты.

## <a name="make-a-geometry-easy-to-update"></a>Упрощение обновления геометрического объекта

Объект `Shape` класса оболочку [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) или [функция](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) и позволяет легко обновлять и обслуживать их.
`new Shape(data: Feature<data.Geometry, any>)` Создает объект shape и инициализирует его с помощью указанного компонента.

<br/>

<iframe height='500' scrolling='no' title='Обновление свойств фигуры' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>обновления свойств фигуры</a> от разработчиков Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода, приведенном выше, создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Точка является [компонентом](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) класса [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Во втором блоке кода инициализируется значение радиуса HTML-элемента "ползунок", затем формируется объект точки и для нее создается оболочка в объекте класса [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest).

В третьем блоке создается функция, которая принимает значение из диапазона HTML-элемента "ползунок" и изменяет значение радиуса с помощью метода [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) класса фигуры.

В четвертом блоке создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Затем точка добавляется к источнику данных.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) отвечает за отображение данных, заключенных в [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), на карте. В третьем блоке кода создается слой многоугольников. Просмотрите свойства слоя многоугольников в разделе о классе [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Создаются и добавляются на карту в источнике данных, hanlder событие click и слой многоугольников [обработчик событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) чтобы убедиться, что точки отображается после загрузки карты, полностью.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Использование выражений стиль на основе данных](data-driven-style-expressions-web-sdk.md)
