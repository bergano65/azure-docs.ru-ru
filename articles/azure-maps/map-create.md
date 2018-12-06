---
title: Создание карты с помощью службы "Карты Azure" | Документация Майкрософт
description: Создание карты на JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 97b94cf54454a83510c5be2cf0d71281dbf5b004
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424245"
---
# <a name="create-a-map"></a>Создание карты

В этой статье показаны способы, позволяющие создать и анимировать карту.  

## <a name="understand-the-code"></a>Изучение кода

Карту можно создать двумя способами. Вы можете навести камеру на карту, указав центральную точку и масштаб изображения, или определив границы камеры по крайним точкам на юго-западе и на северо-востоке.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Настройка камеры

<iframe height='500' scrolling='no' title='Создание карты с помощью CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Создать карту через `CameraOptions` </a>от Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде создается [объект Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) с помощью `new atlas.Map()` и для него задаются центр и масштаб. Свойства карты, такие как положение центральной точки и уровень масштабирования, входят в [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Настройка границ камеры

<iframe height='500' scrolling='no' title='Создание карты с помощью CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Создание карты через `CameraBoundsOptions` </a>от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В представленном выше примере кода создается [объект map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) с помощью `new atlas.Map()`. Свойства карты, например `CameraBoundsOptions`, можно определить через функцию [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) класса Map. Свойства границ и заполнения задаются с помощью `setCamera`.

### <a name="animate-map-view"></a>Просмотр анимации карты

<iframe height='500' scrolling='no' title='Просмотр анимации карты' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Просмотр анимации карты</a> для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

Первый блок в приведенном выше коде создает [объект карты](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) через `new atlas.Map()`. Свойства карты, такие как положение центральной точки и уровень масштабирования, входят в [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions). `CameraOptions` можно определить через конструктор карты или с помощью функции [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) класса Map. [Стиль карты](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) получает значение `road`.

Второй блок кода создает функцию анимации карты, которая анимирует изменения в представлении карты, определяя [AnimationOption](/javascript/api/azure-maps-control/atlas.animationoptions) с помощью функции [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera). Эта функция активируется кнопкой "Анимация карты" и создает случайный уровень масштаба при каждом щелчке мыши.

## <a name="try-out-the-code"></a>Тестирование кода

Давайте рассмотрим подробнее приведенный выше пример. Вы можете редактировать код JavaScript на **вкладке JS** в левой части экрана и сразу видеть изменения в представлении карты на **вкладке "Результат"** справа. Кроме того, вы можете изменить код в CodePen с помощью кнопки **Редактировать в CodePen**.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

См. примеры кодов для добавления функций в приложение:

> [!div class="nextstepaction"]
> [Выбор стиля карты](choose-map-style.md)

> [!div class="nextstepaction"]
> [Добавление элементов управления картой в службе Azure Maps](map-add-controls.md)
