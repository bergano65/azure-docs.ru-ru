---
title: Шаблоны изображений в веб-SDK Azure Maps Карты Microsoft Azure
description: В этой статье вы узнаете, как использовать шаблоны изображений с HTML-маркерами и различными слоями в Веб-SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: f3b1141ea3c3c8e33b8a2ae12c22b6962a90d32b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198230"
---
# <a name="how-to-use-image-templates"></a>Использование шаблонов образов

Изображения могут быть использованы с HTML маркерами и различными слоями в веб-SDK Azure Maps:

 - Слои символа могут отображать точки на карте с помощью значка изображения. Символы также могут быть отображаются вдоль пути линий.
 - Слои полигона могут быть отрисованы с изображением шаблона заполнения. 
 - HTML-маркеры могут отображать точки с помощью изображений и других элементов HTML.

Для обеспечения хорошей производительности слоями загрузите изображения в ресурс спрайта изображения карты перед рендерированием. [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), из SymbolLayer, предварительно загружает пару маркерных изображений в горстке цветов в спрайт изображения карты, по умолчанию. Эти маркерные изображения и многое другое доступны в виде шаблонов SVG. Они могут быть использованы для создания изображений с пользовательскими весами, или использовать в качестве основного и вторичного цвета клиента. Всего предусмотрено 42 шаблона изображений: 27 иконок символов и 15 шаблонов заполнения полигонов.

Шаблоны изображений могут быть добавлены к ресурсам `map.imageSprite.createFromTemplate` спрайта изображения карты с помощью функции. Эта функция позволяет передавать до пяти параметров;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

Это `id` уникальный идентификатор, который вы создаете. Присваивается `id` изображение при добавлении к спрайтам изображения на картах. Используйте этот идентификатор в слоях, чтобы указать, какой ресурс изображения для визуализации. Определяет, `templateName` какой шаблон изображения использовать. Параметр `color` устанавливает основной цвет изображения, а `secondaryColor` параметры задает вторичный цвет изображения. Опция `scale` масштабирует шаблон изображения перед нанесением его на спрайт изображения. Когда изображение применяется к срайту изображения, оно преобразуется в PNG. Для обеспечения четкой визуализации лучше масштабировать шаблон изображения перед добавлением его в спрайт, чем масштабировать его в слой.

Эта функция асинхронно загружает изображение в спрайт изображения. Таким образом, он возвращает Обещание, которое вы можете ждать, пока эта функция будет завершена.

Следующий код показывает, как создать изображение из одного из встроенных шаблонов, и использовать его со слоем символа.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Используйте шаблон изображения со слоем символов

После загрузки шаблона изображения в спрайт изображения карты его можно отобразить в качестве символа в `image` слое `iconOptions`символа, ссылаясь на идентификатор ресурса изображения в варианте .

Следующий образец отображает слой `marker-flat` символа с помощью шаблона изображения с чирок основного цвета и белого вторичного цвета. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Слой символа со встроенным шаблоном значка" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>слой pen Symbol со встроенным шаблоном значка</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Используйте шаблон изображения по пути линий

После загрузки шаблона изображения в спрайт изображения карты его можно отобразить по пути строки, добавив LineString `lineSpacing`в источник данных и используя слой символа `image` с `iconOptions`опцией и ссылаясь на идентификатор ресурса изображения в опции th . 

Следующий образец отображает розовую линию на карте `car` и использует слой символа, используя шаблон изображения с уклонистом синего первичного цвета и белым вторичным цветом. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Линейный слой со встроенным шаблоном значка" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите слой линии pen <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>со встроенным шаблоном значка</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Если шаблон изображения указывает вверх, установите опцию `rotation` значка слоя символа до 90, если вы хотите, чтобы он указал в том же направлении, что и линия.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Используйте шаблон изображения со слоем полигона

После загрузки шаблона изображения в спрайт изображения карты его можно отобразить в виде шаблона заполнения в многогоновом слое, ссылаясь на идентификатор ресурса изображения в `fillPattern` варианте слоя.

Следующий образец отображает многоугольный `dot` слой с использованием шаблона изображения с красным первичным цветом и прозрачным вторичным цветом.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Заполните полигон встроенным шаблоном значка" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите полигон Pen <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>Fill со встроенным шаблоном значка</a> от Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>на <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Установка вторичного цвета шаблонов заполнения облегчает представление основной карты, которая по-прежнему будет обеспечивать основной узор. 

## <a name="use-an-image-template-with-an-html-marker"></a>Используйте шаблон изображения с помощью HTML-маркера

Шаблон изображения можно получить с `altas.getImageTemplate` помощью функции и использовать в качестве содержимого HTML-маркера. Шаблон можно передать в `htmlContent` опцию маркера, а затем `color` `secondaryColor`настроить `text` с помощью, и параметры.

В следующем примере `marker-arrow` используется шаблон с красным первичным цветом, розовым вторичным цветом и текстовым значением "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="HTML Маркер со встроенным шаблоном значка" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите Pen <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>HTML Маркер со встроенным шаблоном значок</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Создание пользовательских многоразовых шаблонов

Если приложение использует один и тот же значок с различными иконками или если вы создаете модуль, который добавляет дополнительные шаблоны изображений, вы можете легко добавить и получить эти значки из веб-SDK Azure Maps. Используйте следующие статические функции в `atlas` пространстве имен.

| name | Тип возвращаемых данных | Описание | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Добавляет пользовательский шаблон изображения SVG в пространство имен атласа. |
|  `getImageTemplate(templateName: string, scale?: number)`| строка | Извлекает шаблон SVG по имени. |
| `getAllImageTemplateNames()` | string[] |  Извлекает шаблон SVG по имени. |

Шаблоны изображений SVG поддерживают следующие значения заполнителя:

| Заполнитель | Описание |
|-|-|
| `{color}` | Основной цвет. | 
| `{secondaryColor}` | Вторичный цвет. | 
| `{scale}` | Изображение SVG преобразуется в изображение png при добавлении на спрайт изображения карты. Этот заполнитель может быть использован для масштабирования шаблона, прежде чем он преобразуется, чтобы убедиться, что он отображает четко. | 
| `{text}` | Местоположение для визуализации текста при использовании с HTML-маркером. |

В следующем примере показано, как взять шаблон SVG и добавить его в веб-SDK Azure Maps в качестве многоразового шаблона значка. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Добавление пользовательского шаблона значка в пространство имен атласа" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите шаблон значка Pen Add в пространство имен<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>атласа</a> по Azure Maps () на <a href='https://codepen.io'>CodePen.</a>
</iframe>

## <a name="list-of-image-templates"></a>Список шаблонов изображений

В этой таблице перечислены все шаблоны изображений, доступные в настоящее время в рамках веб-SDK Azure Maps. Имя шаблона выше каждого изображения. По умолчанию основной цвет синий, а вторичный белый. Чтобы сделать вторичный цвет легче увидеть на белом фоне, следующие изображения имеют вторичный цвет, установленный на черный.

**Шаблоны значков символа**

|||||
|:-:|:-:|:-:|:-:|
| marker | маркер толстый | маркер-круг | маркер-плоский |
|![значок маркера](./media/image-templates/marker.png)|![маркер-толстый значок](./media/image-templates/marker-thick.png)|![значок маркер-круг](./media/image-templates/marker-circle.png)|![маркер-плоский значок](./media/image-templates/marker-flat.png)|
||||
| маркер-квадрат | маркер-квадрат-кластер | маркер-стрелка | маркер-шар-контакт | 
|![маркер квадратный значок](./media/image-templates/marker-square.png)|![значок маркера квадратного кластера](./media/image-templates/marker-square-cluster.png)|![маркер-стрелка значок](./media/image-templates/marker-arrow.png)|![маркер-шар-контакт значок](./media/image-templates/marker-ball-pin.png)|
||||
| маркер-квадрат округленный | маркер-квадрат-округлен-кластер | Флаг | флаг-треугольник |
| ![маркер-квадратокруглая круглая значок](./media/image-templates/marker-square-rounded.png) | ![значок маркер-квадрат-округлен-кластер](./media/image-templates/marker-square-rounded-cluster.png) | ![значок флага](./media/image-templates/flag.png) | ![значок флаг-треугольник](./media/image-templates/flag-triangle.png) |
||||
| треугольник | треугольник толщиной | треугольник-стрелка-вверх | треугольник-стрелка-левый |
| ![значок треугольника](./media/image-templates/triangle.png) | ![треугольник толщиной значок](./media/image-templates/triangle-thick.png) | ![треугольник-стрелка-вверх значок](./media/image-templates/triangle-arrow-up.png) | ![треугольник-стрелка-левый значок](./media/image-templates/triangle-arrow-left.png) |
||||
| Шестиугольник | шестиугольник толщиной | шестиугольник округлые | шестиугольник-круглый толщиной |
| ![шестиугольник значок](./media/image-templates/hexagon.png) | ![шестиугольник толщиной значок](./media/image-templates/hexagon-thick.png) | ![шестиугольник-округленная икона](./media/image-templates/hexagon-rounded.png) | ![шестиугольник-круглый толщиной значок](./media/image-templates/hexagon-rounded-thick.png) |
||||
| закрепление | пин-круглый | округленный квадрат | округлые квадратные толщиной |
| ![значок закрепления](./media/image-templates/pin.png) | ![значок пин-круглый](./media/image-templates/pin-round.png) | ![значок округлого квадрата](./media/image-templates/rounded-square.png) | ![округлые квадратные толщиной значок](./media/image-templates/rounded-square-thick.png) |
||||
| стрелка вверх | стрелка-вверх-тонкий | автомобиль ||
| ![стрелка вверх значок](./media/image-templates/arrow-up.png) | ![стрелка-вверх-тонкий значок](./media/image-templates/arrow-up-thin.png) | ![значок автомобиля](./media/image-templates/car.png) | |

**Шаблоны шаблонов заполнения полигона**

|||||
|:-:|:-:|:-:|:-:|
| Проверки | шашка-поворот | круги | круги-пространство |
| ![значок проверки](./media/image-templates/checker.png) | ![шашка-повернутый значок](./media/image-templates/checker-rotated.png) | ![круги значок](./media/image-templates/circles.png) | ![круговые значок](./media/image-templates/circles-spaced.png) |
|||||
| диагональные линии-вверх | диагональные линии вниз | диагональные полосы-вверх | диагональные полосы вниз |
| ![значок диагональных линий](./media/image-templates/diagonal-lines-up.png) | ![значок диагонали-линии вниз](./media/image-templates/diagonal-lines-down.png) | ![значок диагонали-полосы](./media/image-templates/diagonal-stripes-up.png) | ![значок диагонали-полосы вниз](./media/image-templates/diagonal-stripes-down.png) |
|||||
| сетки-линии | вращающиеся линии сетки | вращающиеся-сетки-полосы | x-заполнить |
| ![значок сетки линий](./media/image-templates/grid-lines.png) | ![Значок вращающихся линий сетки](./media/image-templates/rotated-grid-lines.png) | ![вращаемый-сетка-полосы значок](./media/image-templates/rotated-grid-stripes.png) | ![x-заполнить значок](./media/image-templates/x-fill.png) |
|||||
| зигзаг | зигзаг-вертикаль | Точки |  |
| ![зигзагообразная икона](./media/image-templates/zig-zag.png) | ![зигзагообразно-вертикальная икона](./media/image-templates/zig-zag-vertical.png) | ![значок точек](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Попробуйте сейчас инструмент

С помощью следующего инструмента можно различными способами внедренировать различные встроенные шаблоны изображений и настраивать первичные и вторичные цвета и масштаб.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Параметры шаблона значок" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>параметры шаблона pen Icon</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>по Картам Azure () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [атласное пространство имен](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Смотрите следующие статьи для получения дополнительных образцов кода, где можно использовать шаблоны изображений:

> [!div class="nextstepaction"]
> [Добавление слоя символов](map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление слоя линий](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](map-add-shape.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков на карту](map-add-bubble-layer.md)
