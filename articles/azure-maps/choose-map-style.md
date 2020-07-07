---
title: Изменение стиля схемы в Azure Maps | Карты Microsoft Azure
description: В этой статье вы узнаете о функциональных возможностях, связанных с стилями, доступных в веб-пакете SDK для Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335685"
---
# <a name="change-the-style-of-the-map"></a>Изменение стиля карты

Карта поддерживает несколько различных [параметров стиля](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) , которые можно задать при инициализации карты или позже с помощью `setStyle` функции Maps. В этой статье показано, как использовать эти параметры стиля для настройки внешнего вида карт. Научитесь задавать стиль при загрузке схемы и научиться устанавливать новый стиль карт с помощью элемента управления "Выбор стиля".

## <a name="set-the-style-options"></a>Задание параметров стиля 

Параметры стиля можно передать в сопоставление при инициализации или обновлении позже с помощью `setStyle` функции Maps.

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

В следующем средстве показано, как различные параметры стиля изменяют способ отрисовки карт. Чтобы увидеть трехмерные строения, увеличивайте масштаб до основного города. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Параметры стиля карт" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Параметры стиля отображения</a> перьев по Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Выбор базового стиля схемы

Один из наиболее распространенных параметров стиля схемы используется для изменения стиля базовой схемы, для которой задан стиль. Многие [поддерживаемые стили карт в Azure Maps](supported-map-styles.md) доступны в веб-пакете SDK. 

### <a name="set-base-map-style-on-map-load"></a>Задать стиль базовой схемы для загрузки карт


Стиль Map можно указать при инициализации схемы, задав `style` параметр. В следующем коде `style` параметру Map присваивается значение `grayscale_dark` On Initialization.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Установка стиля при загрузке карты' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Установка стиля при загрузке карты</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Обновление базового стиля Map

 Стиль схемы можно обновить с помощью `setStyle` функции и присвоив `style` параметру нужный стиль отображения.

```javascript
map.setStyle({ style: 'satellite' });
```

В следующем коде после загрузки экземпляра Map стиль схемы обновляется с `road` на с `satellite` помощью функции [SetStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='Обновление стиля' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Обновление стиля</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Добавление средства выбора стиля

Элемент управления Выбор стиля позволяет легко использовать кнопку с раскрывающейся панелью, которая может использоваться конечным пользователем для изменения стиля отображения. Средство выбора стиля имеет два разных варианта макета. По умолчанию средство выбора стиля использует `icons` Макет и отображает все стили карт в виде горизонтальной строки значков. 

<center>

![Макет значка выбора стиля](media/choose-map-style/style-picker-icon-layout.png)</center>

Второй параметр макета вызывается `list` и отображает прокручиваемый список стилей карт.  

<center>

![Макет списка средств выбора стилей](media/choose-map-style/style-picker-list-layout.png)</center>


В следующем коде показано, как создать экземпляр элемента управления "Выбор стиля" и добавить его в правый верхний угол на карте. В средстве выбора стиля установлен Темный стиль и отображается несколько стилей карт с помощью слоя списка.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Следующий код добавляет элемент управления Выбор стиля с параметрами по умолчанию на карту, чтобы пользователь мог легко переключаться между различными стилями карт. Переключите стиль карт, используя элемент управления стиля карт, рядом с верхним правым углом.

<br/>

<iframe height='500' scrolling='no' title='Добавление элемента выбора стиля' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Добавление элемента выбора стиля</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> По умолчанию при использовании ценовой категории S0 Azure Maps элемент управления Выбор стиля перечисляет все доступные стили. Если требуется сократить число стилей в этом списке, передайте массив стилей, которые должны отображаться в списке, в `mapStyle` параметр выбора стиля. Если вы используете S1 и хотите отобразить все доступные стили, задайте `mapStyles` для параметра Выбор стиля значение `"all"` .

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, используемых в этой статье.

> [!div class="nextstepaction"]
> [Схема](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [стилеоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol).

> [!div class="nextstepaction"]
> [стилеконтролоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Добавление элементов управления в карты:

> [!div class="nextstepaction"]
> [Добавление элементов управления картой в службе Azure Maps](map-add-controls.md)

> [!div class="nextstepaction"]
> [Добавление ПИН-кода](map-add-pin.md)
