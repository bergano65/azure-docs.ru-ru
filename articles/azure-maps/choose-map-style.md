---
title: Измените стиль карты в Azure Maps Карты Microsoft Azure
description: В этой статье вы узнаете о функциональных возможностях, связанных со стилем, доступных в веб-SDK Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335685"
---
# <a name="change-the-style-of-the-map"></a>Изменение стиля карты

Карта поддерживает несколько различных [вариантов стиля,](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) которые могут быть установлены, когда карта инициализирована или позже с помощью функции карт. `setStyle` В этой статье показано, как использовать эти параметры стиля для настройки внешнего вида карт. Научитесь устанавливать стиль при загрузке карты и научитесь устанавливать новый стиль карты с помощью управления сборщиком стилей.

## <a name="set-the-style-options"></a>Установите параметры стиля 

Параметры стиля могут быть переданы на карту, когда она `setStyle` инициализирована или обновлена позже с помощью функции карт.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

Следующий инструмент показывает, как различные параметры стиля меняют способ отображения карты. Чтобы увидеть 3D здания, увеличьте масштаб в непосредственной близости от крупного города. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Параметры стиля карты" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>параметры стиля Pen Map</a> по<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Выберите стиль базовой карты

Один из наиболее распространенных вариантов стиля карты используется для изменения стиля базовой карты, которая стилизована. Многие [из поддерживаемых стилей карт в Azure Maps](supported-map-styles.md) доступны в Web SDK. 

### <a name="set-base-map-style-on-map-load"></a>Установите стиль базовой карты на загрузке карты


Стиль карты может быть определен при инициализации карты путем настройки опции. `style` В следующем коде `style` опция карты установлена `grayscale_dark` на инициализацию.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Установка стиля при загрузке карты' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Установка стиля при загрузке карты</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Обновление базового стиля карты

 Стиль карты можно обновить `setStyle` с помощью `style` функции и установить опцию в желаемом стиле карты.

```javascript
map.setStyle({ style: 'satellite' });
```

В следующем коде после загрузки экземпляра карты стиль `road` карты `satellite` обновляется с помощью функции [setStyle.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-)

<br/>

<iframe height='500' scrolling='no' title='Обновление стиля' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Обновление стиля</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Добавление средства выбора стиля

Управление сборщиком стилей обеспечивает простую в использовании кнопку с панелью вылетов, к которым может использоваться конечный пользователь для изменения стиля карты. Сборщик стилей имеет два различных варианта компоновки. По умолчанию сборщик `icons` стилей использует макет и отображает весь стиль карты в виде горизонтального ряда иконок. 

<center>

![Стиль сборщик значок макет](media/choose-map-style/style-picker-icon-layout.png)</center>

Называется второй вариант `list` компоновки, который отображает прокрутки стилей карт.  

<center>

![Расположение списка список сборщиков стилей](media/choose-map-style/style-picker-list-layout.png)</center>


Следующий код показывает, как создать экземпляр управления сборщиком стилей и добавить его в верхний правый угол карты. Сборщик стилей установлен, чтобы иметь темный стиль и показать несколько стилей карты с помощью слоя списка.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Следующий код добавляет управление сборщиком стилей с настройками по умолчанию на карту, так что пользователь может легко переключаться между различными стилями карты. Переключите стиль карты, используя управление стилем карты в верхнем правом углу.

<br/>

<iframe height='500' scrolling='no' title='Добавление элемента выбора стиля' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Добавление элемента выбора стиля</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> По умолчанию при использовании ценового уровня S0 ВАЗа Azure Maps в управлении сборщиком стилей перечислены все доступные стили. Если вы хотите уменьшить количество стилей в этом списке, передайте массив стилей, которые вы хотите появиться в списке, в `mapStyle` опцию сборщика стилей. Если вы используете S1 и хотите показать все `mapStyles` доступные стили, `"all"`установите опцию сборщика стилей.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, используемых в этой статье.

> [!div class="nextstepaction"]
> [Карта](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol).

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Добавьте элементы управления на свои карты:

> [!div class="nextstepaction"]
> [Добавление элементов управления картой в службе Azure Maps](map-add-controls.md)

> [!div class="nextstepaction"]
> [Добавление ПИН-кода](map-add-pin.md)
