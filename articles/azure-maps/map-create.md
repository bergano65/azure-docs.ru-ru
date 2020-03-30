---
title: Создайте карту с помощью Azure Maps Карты Microsoft Azure
description: В этой статье вы узнаете, как отобразить карту на веб-странице с помощью веб-SDK Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cfeff430e5313c8728582c4790c9aca9482d63aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534923"
---
# <a name="create-a-map"></a>Создание карты

В этой статье показаны способы, позволяющие создать и анимировать карту.  

## <a name="loading-a-map"></a>Загрузка карты

Чтобы загрузить карту, создайте новый экземпляр [класса Карты.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) При инициализации карты передайте ID элемента DIV, чтобы сделать карту и передать набор опций для использования при загрузке карты. Если информация об аутентификации по умолчанию не указана в пространстве `atlas` имен, эта информация должна быть указана в вариантах карты при загрузке карты. Карта загружает несколько ресурсов асинхронно для производительности. Таким образом, после создания экземпляра `ready` `load` карты прикрепите событие к карте, а затем добавьте любой дополнительный код, который взаимодействует с картой, в обработчик событий. Событие `ready` запускается, как только карта имеет достаточно ресурсов, загруженных для взаимодействия с программным. Событие `load` сражается после того, как первоначальный вид карты полностью завершил загрузку. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Базовая загрузка карты" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>загрузку карты</a> Pen Basic<a href='https://codepen.io/azuremaps'>@azuremaps</a>по Картам Azure () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Вы можете загрузить несколько карт на одной странице. Несколько карт на одной странице могут использовать одни и те же или различные настройки аутентификации и языка.

## <a name="show-a-single-copy-of-the-world"></a>Показать одну копию мира

Когда карта будет увеличена на широкий экран, несколько копий мира будут отображаться горизонтально. Этот вариант отлично подходит для некоторых сценариев, но для других приложений желательно увидеть одну копию мира. Такое поведение реализуется `renderWorldCopies` путем `false`настройки опции карты.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies - ложные" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies - ложные</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>по Azure Maps ( ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Параметры карты

При создании карты есть несколько различных типов опций, которые могут быть переданы, чтобы настроить, как карта функционирует, как указано ниже.

- Для указания области, указанной на карте, используются [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) и [CameraBoundOptions.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions)
- [Варианты обслуживания](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) используются для определения того, как карта должна взаимодействовать с службами, которые питают карту.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) используются для указания карты должны быть стилизованы и визуализированы.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) используются для определения того, как карта должна понижаться при взаимодействии пользователя с картой. 

Эти параметры также могут быть обновлены после `setCamera`того, `setStyle`как `setUserInteraction` карта была загружена с помощью, `setServiceOptions`и функции. 

## <a name="controlling-the-map-camera"></a>Управление картой камеры

Существует два способа установить отображаемую область карты с помощью камеры карты. Вы можете настроить параметры камеры при загрузке карты. Или вы можете `setCamera` вызвать опцию в любое время после загрузки карты для программного обновления представления карты.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Настройка камеры

Камера карты контролирует то, что отображается в поле зрения холста карты. Параметры камеры могут передаваться в параметры карты при инициализании или передавались в функцию карт. `setCamera`

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

В следующем коде создается [объект Карты](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) и параметры центра и масштабирования. Свойства карты, такие как уровень центра и масштабирования, являются частью [CameraOptions.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)

<br/>

<iframe height='500' scrolling='no' title='Создание карты с помощью CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Создать карту через `CameraOptions` </a>от Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Настройка границ камеры

Для обновления камеры карты можно использовать ограничительную коробку. Если ящик для заграничных данных был рассчитан на основе точечных данных, часто полезно также указать значение обивки пикселя в вариантах камеры для учета размера значка. Это поможет гарантировать, что точки не упадут с края обзора карты.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

В следующем коде [объект Карты](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) строится через `new atlas.Map()`. Свойства карты, например `CameraBoundsOptions`, можно определить через функцию [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) класса Map. Свойства границ и заполнения задаются с помощью `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Создание карты с помощью CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Создание карты через `CameraBoundsOptions` </a>от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Просмотр анимации карты

При настройке параметры камеры карты также могут быть [установлены параметры анимации.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) Эти параметры определяют тип анимации и продолжительность, которые она должна принять для перемещения камеры.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

В следующем коде первый код-блок создает карту и устанавливает стили ввода и масштабирования карт. Во втором блоке кода для кнопки аниматора создается обработчик события щелчка. При нажатии на эту `setCamera` кнопку функция вызывается с некоторыми случайными значениями для [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) и [AnimationOptions.](/javascript/api/azure-maps-control/atlas.animationoptions)

<br/>

<iframe height='500' scrolling='no' title='Просмотр анимации карты' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Просмотр анимации карты</a> для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Тестирование кода

Посмотрите на образцы кода. Вы можете отсеить код JavaScript в **вкладке JS** и увидеть изменения представления карты на **вкладке «Результат».** Вы также можете нажать **Edit на CodePen**, в правом верхнем углу, и изменить код в CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Карта](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [КамераВарианты](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [АнимацияВарианты](/javascript/api/azure-maps-control/atlas.animationoptions)

См. примеры кодов для добавления функций в приложение:

> [!div class="nextstepaction"]
> [Изменение стиля карты](choose-map-style.md)

> [!div class="nextstepaction"]
> [Добавление элементов управления на карту](map-add-controls.md)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)
