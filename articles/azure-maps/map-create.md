---
title: Создание карты с помощью службы "Карты Azure" | Документация Майкрософт
description: Создание карты на JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9759c4149c6b026837e550dcf3ab0a0156bbb736
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730015"
---
# <a name="create-a-map"></a>Создание карты

В этой статье показано, как создать карту.  

## <a name="understand-the-code"></a>Изучение кода

Карту можно создать двумя способами. Вы можете задать камеру карты с помощью центральной точки и масштаба. Задайте границы камеры на карте, указав юго-западную и северо-восточную точки границ.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Настройка камеры

<iframe height='310' scrolling='no' title='Создание карты с помощью CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Создать карту через `CameraOptions` </a>от Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В представленном выше примере кода создается [объект map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) с помощью `new atlas.Map()`. Свойства карты, такие как положение центральной точки и уровень масштабирования, входят в [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). `CameraOptions` можно определить через конструктор карты или с помощью функции [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) класса Map.

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Настройка границ камеры

<iframe height='310' scrolling='no' title='Создание карты с помощью CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Создание карты через `CameraBoundsOptions` </a>от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В представленном выше примере кода создается [объект map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) с помощью `new atlas.Map()`. Свойства карты, например расположение ограничивающего прямоугольника, входят в [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest). `CameraBoundsOptions` можно определить с помощью функции [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) класса Map.

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
