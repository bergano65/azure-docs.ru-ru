---
title: Шаблоны изображений в Azure Maps веб-пакете SDK | Карты Microsoft Azure
description: В этой статье вы узнаете, как использовать шаблоны изображений с маркерами HTML и различными слоями в веб-пакете SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: ee8e8ee4ca64de0390b6fa34e36fb4d06348a8ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804815"
---
# <a name="how-to-use-image-templates"></a>Использование шаблонов образов

Изображения можно использовать с маркерами HTML и различными слоями в Azure Maps веб-пакете SDK:

 - Слои символов позволяют отображать точки на карте с помощью значка изображения. Символы также можно визуализировать по линиям.
 - Слои многоугольников можно визуализировать с помощью изображения шаблона заливки. 
 - Маркеры HTML могут отображать точки с помощью изображений и других элементов HTML.

Чтобы обеспечить хорошую производительность при работе с слоями, перед отрисовкой Загрузите изображения в ресурс с изображением Sprite. [Иконоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)(симболлайер) предварительно загружает несколько изображений маркера в несколько цветов в спрайт изображения Map по умолчанию. Эти изображения маркеров и многое другое доступны в виде шаблонов SVG. Они могут использоваться для создания изображений с настраиваемыми масштабами или использования в качестве основного и дополнительного цветов клиента. В итоге предусмотрено 42 шаблонов изображений: 27 значков символов и 15 узоров заливки многоугольников.

Шаблоны изображений можно добавить в ресурсы с изображением Sprite с помощью `map.imageSprite.createFromTemplate` функции. Эта функция позволяет передавать до пяти параметров;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id` — Это уникальный идентификатор, который вы создаете. `id` Назначается образу при его добавлении в спрайт изображений Maps. Используйте этот идентификатор в слоях, чтобы указать, какой ресурс изображения следует визуализировать. `templateName` Указывает используемый шаблон изображения. `color` Параметр задает основной цвет изображения, а `secondaryColor` параметры задают вторичный цвет изображения. `scale` Параметр масштабирует шаблон изображения перед его применением к спрайту изображений. Когда изображение применяется к спрайту изображений, оно преобразуется в PNG. Чтобы обеспечить четкое отображение, лучше масштабировать шаблон изображения перед добавлением его к спрайту, чем масштабировать его в слое.

Эта функция асинхронно загружает изображение в спрайт изображений. Таким же методом возвращается обещание, которое можно ожидать завершения этой функции.

В следующем коде показано, как создать изображение из одного из встроенных шаблонов и использовать его с уровнем символов.

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

## <a name="use-an-image-template-with-a-symbol-layer"></a>Использование шаблона изображения с уровнем символов

После загрузки шаблона изображения в спрайт изображений карт его можно визуализировать как символ на уровне символов, ссылаясь на идентификатор ресурса изображения в `image` параметре. `iconOptions`

В следующем примере слой символов визуализируется с помощью шаблона `marker-flat` изображения с бирюзовым основным цветом и белым вторичным цветом. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Слой символов с встроенным шаблоном значка" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>слой символов пера со встроенным шаблоном значка</a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Использование шаблона изображения вдоль строкового пути

После загрузки шаблона изображения в спрайт изображений карт его можно визуализировать по пути линии, добавив LineString к источнику данных и используя слой символов с `lineSpacing`параметром и ОБРАТИВШИСЬ к идентификатору ресурса изображения в `image` параметре TH. `iconOptions` 

Следующий пример выводит розовую линию на карте и использует слой символов с помощью шаблона `car` изображения с синим основным цветом тускло-Васильковый и белым вторичным цветом. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Слой линий со встроенным шаблоном значка" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>слой линий пера со встроенным шаблоном значка</a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() на <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Если вы указываете шаблон изображения, установите для `rotation` параметра значок на уровне символов значение 90, если хотите, чтобы он указывал на то же направление, что и линия.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Использование шаблона изображения с слоем многоугольников

После загрузки шаблона изображения в спрайт изображений карт его можно визуализировать как шаблон заливки на слое многоугольников, ссылаясь на идентификатор ресурса изображения в `fillPattern` параметре слоя.

В следующем примере слой многоугольников визуализируется с помощью `dot` шаблона изображения с красным основным цветом и прозрачным вторичным цветом.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Заливка многоугольника с помощью встроенного шаблона значка" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>Заливка многоугольника с помощью встроенного шаблона значка</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Установка вторичного цвета шаблонов заливки упрощает просмотр базовой схемы, тем самым предоставляя первичный шаблон. 

## <a name="use-an-image-template-with-an-html-marker"></a>Использование шаблона изображения с маркером HTML

Шаблон изображения можно получить с помощью `altas.getImageTemplate` функции и использовать в качестве содержимого HTML-маркера. Шаблон `htmlContent` можно передать в параметр маркера, а затем настроить с помощью параметров `color`, `secondaryColor`и. `text`

В следующем примере используется `marker-arrow` шаблон с красным основным цветом, розовым вторичным цветом и текстовым значением "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="HTML-маркер со встроенным шаблоном значка" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Просмотрите HTML- <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>маркер пера со встроенным шаблоном значка</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Создание настраиваемых многократно используемых шаблонов

Если приложение использует один и тот же значок с разными значками или если вы создаете модуль, который добавляет дополнительные шаблоны изображений, вы можете легко добавлять и получать эти значки из веб-пакета SDK Azure Maps. Используйте следующие статические функции в `atlas` пространстве имен.

| Имя | Тип возвращаемых данных | Описание | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Добавляет пользовательский шаблон изображения SVG в пространство имен Atlas. |
|  `getImageTemplate(templateName: string, scale?: number)`| строка | Извлекает шаблон SVG по имени. |
| `getAllImageTemplateNames()` | string[] |  Извлекает шаблон SVG по имени. |

Шаблоны изображений SVG поддерживают следующие значения заполнителей:

| Заполнитель | Описание |
|-|-|
| `{color}` | Основной цвет. | 
| `{secondaryColor}` | Вторичный цвет. | 
| `{scale}` | Изображение SVG преобразуется в изображение PNG при добавлении в спрайт изображений карт. Этот заполнитель можно использовать для масштабирования шаблона перед его преобразованием, чтобы обеспечить его четкое отображение. | 
| `{text}` | Расположение для отрисовки текста при использовании с маркером HTML. |

В следующем примере показано, как взять шаблон SVG и добавить его в Azure Maps веб-пакет SDK в качестве шаблона многократно используемого значка. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Добавление пользовательского шаблона значка в пространство имен Atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Добавление пользовательского шаблона значка в пространство имен Atlas</a> с<a href='https://codepen.io/azuremaps'>@azuremaps</a>помощью Azure Maps () в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Список шаблонов изображений

В этой таблице перечислены все шаблоны изображений, доступные в данный момент в Azure Maps веб-пакете SDK. Имя шаблона выше каждого изображения. По умолчанию основным цветом является синий цвет, а дополнительный — белым. Чтобы сделать дополнительный цвет более удобным для просмотра на белом фоне, на следующих изображениях дополнительный цвет устанавливается в черный.

**Шаблоны значков символов**

|||||
|:-:|:-:|:-:|:-:|
| marker | жирный маркер | маркер-окружность | Плоский маркер |
|![значок маркера](./media/image-templates/marker.png)|![значок жирного маркера](./media/image-templates/marker-thick.png)|![значок "маркер-окружность"](./media/image-templates/marker-circle.png)|![значок с плоским маркером](./media/image-templates/marker-flat.png)|
||||
| квадратный маркер | маркер-квадратный — кластер | стрелка-маркер | маркер-шарик-закрепление | 
|![значок квадратного маркера](./media/image-templates/marker-square.png)|![значок "маркер — квадратный — кластер"](./media/image-templates/marker-square-cluster.png)|![значок стрелки-маркера](./media/image-templates/marker-arrow.png)|![значок "маркер-шарик-закрепление"](./media/image-templates/marker-ball-pin.png)|
||||
| маркер-квадратное округление | маркер-квадратный-Скругленный — кластер | Флаг | Flag-треугольник |
| ![значок "маркер-квадрат-Скругленный"](./media/image-templates/marker-square-rounded.png) | ![значок "маркер-квадрат-Скругленный — кластер"](./media/image-templates/marker-square-rounded-cluster.png) | ![значок флага](./media/image-templates/flag.png) | ![значок флага-треугольник](./media/image-templates/flag-triangle.png) |
||||
| треугольник | треугольник — толстый | стрелка на треугольник вверх | треугольная стрелка — слева |
| ![значок треугольника](./media/image-templates/triangle.png) | ![значок с толстым треугольником](./media/image-templates/triangle-thick.png) | ![значок стрелки "треугольник"](./media/image-templates/triangle-arrow-up.png) | ![значок стрелки влево](./media/image-templates/triangle-arrow-left.png) |
||||
| шестиугольник | шестиугольник — толстая | шестиугольник — округленный | шестиугольник-Скругленный-толстый |
| ![значок шестиугольника](./media/image-templates/hexagon.png) | ![шестиугольник — толстый значок](./media/image-templates/hexagon-thick.png) | ![значок в кружке](./media/image-templates/hexagon-rounded.png) | ![значок в шестиугольнике-закругленный — толстый](./media/image-templates/hexagon-rounded-thick.png) |
||||
| pin | закрепить-округлить | Скругленный квадрат | скругленное-квадратное-толстое |
| ![значок закрепления](./media/image-templates/pin.png) | ![значок "закрепить на круге"](./media/image-templates/pin-round.png) | ![значок скругленного квадрата](./media/image-templates/rounded-square.png) | ![значок со скругленной квадратной линией](./media/image-templates/rounded-square-thick.png) |
||||
| Стрелка вверх | стрелка — тонкая | автомобиль ||
| ![значок стрелки "вверх"](./media/image-templates/arrow-up.png) | ![Стрелка-вверх-тонкий значок](./media/image-templates/arrow-up-thin.png) | ![значок автомобиля](./media/image-templates/car.png) | |

**Шаблоны узорной заливки многоугольников**

|||||
|:-:|:-:|:-:|:-:|
| программы | повернутый | круги | круги — с интервалами |
| ![значок проверки](./media/image-templates/checker.png) | ![значок, повернутый в шашку](./media/image-templates/checker-rotated.png) | ![значок кругов](./media/image-templates/circles.png) | ![значок "кружки — пробел"](./media/image-templates/circles-spaced.png) |
|||||
| по диагонали вверх | диагональные линии вниз | диагональные полосы | диагональные полосы-вниз |
| ![значок "диагональные линии"](./media/image-templates/diagonal-lines-up.png) | ![значок "диагональные линии"](./media/image-templates/diagonal-lines-down.png) | ![значок "диагональные полосы"](./media/image-templates/diagonal-stripes-up.png) | ![значок "диагональные полосы"](./media/image-templates/diagonal-stripes-down.png) |
|||||
| Сетка-линии | повернутый-Grid-Line | повернутый-Grid-полосковые полосы | Заливка x |
| ![значок сетки — линии](./media/image-templates/grid-lines.png) | ![значок "повернутый-Grid-Line"](./media/image-templates/rotated-grid-lines.png) | ![значок "повернутый-Grid-полоск"](./media/image-templates/rotated-grid-stripes.png) | ![значок "x-Fill"](./media/image-templates/x-fill.png) |
|||||
| зигзаг — зигзаг | зигзаг-зигзаг-по вертикали | количество |  |
| ![значок зигзаг-зигзаг](./media/image-templates/zig-zag.png) | ![значок зигзаг-зигзаг-Vertical](./media/image-templates/zig-zag-vertical.png) | ![значок точек](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Попробуйте это средство

С помощью следующего средства можно отобразить различные встроенные шаблоны изображений различными способами и настроить основной и дополнительный цвета и масштаб.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Параметры шаблона значка" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Сведения о <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>параметрах шаблона значок</a> пера см.<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [имажеспритеманажер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [пространство имен Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Дополнительные примеры кода, в которых можно использовать шаблоны изображений, см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление слоя символов](map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление слоя линий](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](map-add-shape.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков на карту](map-add-bubble-layer.md)
