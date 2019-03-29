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
ms.openlocfilehash: 2582b277238bbfbda29156c857e7bd91cf6fe059
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579297"
---
# <a name="add-a-shape-to-a-map"></a>Добавление фигуры на карту

В этой статье показано, как добавить [фигуру](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) на карту и обновить свойства существующей фигуры на карте.

<a id="addALine"></a>

## <a name="add-a-line"></a>Добавление линии

<iframe height='500' scrolling='no' title='Добавление линии на карту' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Добавление линии на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Объект [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) создается и добавляется к источнику данных.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) отвечает за отображение линейных объектов в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Последний блок кода позволят создать и добавить на карту слой линий. Ознакомьтесь со свойствами слоя линий в разделе о классе [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Источник данных и слой линий создаются и добавляются на карту с помощью функции [прослушивателя событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Это обеспечивает отображение линий только после полной загрузки карты.

## <a name="add-symbols-along-a-line"></a>Добавьте символы вдоль линии

В этом примере показано, как добавлять значки-стрелки вдоль линии на карте. Если с помощью слоя символ, значение «размещение» возможность «строка», будет отображать символы вдоль линии и поворот значки (0 градусов = right).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Показать стрелку вдоль линии" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Показать стрелку вдоль линии</a> по "карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Настройка слоя линий

Слой линий имеет несколько параметров стиля, которые можно опробовать с помощью определенного средства.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя линий' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода с <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>параметрами слоя линий</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Добавление круга

<iframe height='500' scrolling='no' title='Добавление круга на карту' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Добавление круга на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Круг — это объект [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) класса [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest), у которого есть свойство `subType` со значением circle и измерителями значений свойства `radius`. Когда объект Point со свойством subType круга добавляется в источник данных, выполняется преобразование в циклический многоугольник в пределах карты.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) отвечает за отображение данных, заключенных в [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), на карте. Последний блок кода позволяет создать и добавить на карту слой многоугольников. Просмотрите свойства слоя многоугольников в разделе о классе [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Источник данных и слой многоугольников создаются и добавляются на карту с помощью функции [прослушивателя событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Это обеспечивает отображение кругов только после полной загрузки карты.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Добавление многоугольника

Есть два способа добавления многоугольника на карту. Оба они описаны в приведенных ниже примерах.

### <a name="use-polygon-layer"></a>Использование слоя многоугольников 

<iframe height='500' scrolling='no' title='Добавление многоугольника на карту ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Добавление многоугольника на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). [Многоугольник](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) создается на основе массива координат и добавляется к источнику данных. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) отвечает за отображение данных, заключенных в [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), на карте. Последний блок кода позволяет создать и добавить на карту слой многоугольников. Просмотрите свойства слоя многоугольников в разделе о классе [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Источник данных и слой многоугольников создаются и добавляются на карту с помощью функции [прослушивателя событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Это обеспечивает отображение многоугольников только после полной загрузки карты.

### <a name="use-polygon-and-line-layer"></a>Использование слоев многоугольников и линий

<iframe height='500' scrolling='no' title='Слои многоугольников и линий для добавления многоугольника' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода для <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>добавления многоугольников с помощью слоев многоугольников и линий</a> от разработчиков Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). [Многоугольник](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) создается на основе массива координат и добавляется к источнику данных. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) отвечает за отображение данных, заключенных в [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), на карте. Просмотрите свойства слоя многоугольников в разделе о классе [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) представляет собой массив линий. Ознакомьтесь со свойствами слоя линий в разделе о классе [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Третий блок кода позволяет создать слои многоугольников и линий.

В последнем блоке кода слои многоугольников и линий добавляются на карту. Источник данных и слои создаются и добавляются на карту с помощью функции [прослушивателя событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Это обеспечивает отображение многоугольников только после полной загрузки карты.

## <a name="fill-a-polygon-with-a-pattern"></a>Заполнение многоугольника с помощью шаблона

Кроме заполнения многоугольник цветом можно использоваться шаблон образа. Загрузить шаблон изображения в ресурсы спрайт изображения карты и затем ссылаться на этот образ с `fillPattern` свойства слоя многоугольников.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Шаблон многоугольника fill" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>шаблона многоугольника fill</a> по "карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-layer"></a>Настройка слоя многоугольников

Слой прямоугольников имеет несколько параметров стиля, которые можно опробовать с помощью определенного средства.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpgL</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-a-shape"></a>Обновление фигур

Класс Shape позволяет создать оболочку для [геометрии](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) и [компонента](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) и упрощает их обновление и обслуживание.
`new Shape(data: Feature<data.Geometry, any>)` отвечает за создание объекта фигуры и его инициализацию с конкретным компонентом.

<br/>

<iframe height='500' scrolling='no' title='Обновление свойств фигуры' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>обновления свойств фигуры</a> от разработчиков Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода, приведенном выше, создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Точка является [компонентом](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) класса [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Во втором блоке кода инициализируется значение радиуса HTML-элемента "ползунок", затем формируется объект точки и для нее создается оболочка в объекте класса [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest).

В третьем блоке создается функция, которая принимает значение из диапазона HTML-элемента "ползунок" и изменяет значение радиуса с помощью метода [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) класса фигуры.

В четвертом блоке создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Затем точка добавляется к источнику данных.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) отвечает за отображение данных, заключенных в [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), на карте. В третьем блоке кода создается слой многоугольников. Просмотрите свойства слоя многоугольников в разделе о классе [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Источник данных, прослушиватель событий щелчка и слой многоугольников создаются и добавляются на карту с помощью функции [прослушивателя событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Это обеспечивает отображение точек только после полной загрузки карты.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Маркеры HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Добавление слоя карты рисков](./map-add-heat-map-layer.md)
