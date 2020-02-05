---
title: Создание схемы с помощью Azure Maps | Карты Microsoft Azure
description: Из этой статьи вы узнаете, как визуализировать карту на веб-странице с помощью веб-пакета SDK Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 578abae5b206b31674b00b9d27ef34174b93759f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988589"
---
# <a name="create-a-map"></a>Создание карты

В этой статье показаны способы, позволяющие создать и анимировать карту.  

## <a name="loading-a-map"></a>Загрузка схемы

Чтобы загрузить карту, создайте новый экземпляр [класса Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). При инициализации карты передайте идентификатор элемента DIV для отрисовки карты и передайте набор параметров для использования при загрузке карты. Если сведения о проверке подлинности по умолчанию не указаны в пространстве имен `atlas`, эти сведения необходимо указать в параметрах Map при загрузке схемы. Эта схема асинхронно загружает несколько ресурсов для повышения производительности. Таким образом, после создания экземпляра Map присоедините к карте событие `ready` или `load`, а затем добавьте дополнительный код, взаимодействующий с картой с обработчиком событий. Событие `ready` срабатывает, как только на карте загружено достаточно ресурсов для взаимодействия с программным способом. Событие `load` срабатывает после полного завершения загрузки представления первоначальной таблицы. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Базовая Загрузка карт" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел " <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Базовая схема</a> перьевого отображения", Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> На одной странице можно загрузить несколько карт. Несколько карт на одной странице могут использовать одинаковые или разные параметры проверки подлинности и языка.

## <a name="show-a-single-copy-of-the-world"></a>Отображение одной копии мира

Когда на широком экране отображается изображение, несколько копий мира отображаются по горизонтали. Этот вариант прекрасно подходит для некоторых сценариев, но для других приложений желательно просмотреть единую копию мира. Это поведение реализуется путем установки параметра Maps `renderWorldCopies` в значение `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Рендерворлдкопиес = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Ознакомьтесь с пером <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>рендерворлдкопиес = false</a> , Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Управление камерой карт

Существует два способа задать отображаемую область на карте с помощью камеры на карте. При загрузке схемы можно задать параметры камеры. Или можно вызвать `setCamera` параметр в любое время после загрузки картой, чтобы программно обновить представление Map.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Настройка камеры

В следующем коде создается [объект Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) , а также устанавливаются параметры центрирования и масштабирования. Свойства карт, такие как выравнивание по центру и масштабу, являются частью [камераоптионс](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Создание карты с помощью CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите, <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>как перо создает карту с помощью `CameraOptions` </a>Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Настройка границ камеры

В следующем коде [объект Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) создается с помощью `new atlas.Map()`. Свойства карты, например `CameraBoundsOptions`, можно определить через функцию [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) класса Map. Свойства границ и заполнения задаются с помощью `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Создание карты с помощью CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите, <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>как перо создает карту с помощью `CameraBoundsOptions` </a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Просмотр анимации карты

В следующем коде первый блок кода создает карту и задает стили карт ввода и масштабирования. Во втором блоке кода для кнопки анимировать создается обработчик событий нажатия. При нажатии этой кнопки функция `setCamera` вызывается с некоторыми случайными значениями для [камераоптионс](/javascript/api/azure-maps-control/atlas.cameraoptions) и [аниматионоптионс](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Просмотр анимации карты' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Просмотр анимации карты</a> для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Тестирование кода

Взгляните на примеры кода. Код JavaScript можно изменить на **вкладке JS** и просмотреть изменения в представлении Map на **вкладке результат**. Можно также щелкнуть **изменить в CodePen**в правом верхнем углу и изменить код в CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Схема](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [камераоптионс](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [аниматионоптионс](/javascript/api/azure-maps-control/atlas.animationoptions)

См. примеры кодов для добавления функций в приложение:

> [!div class="nextstepaction"]
> [Изменение стиля схемы](choose-map-style.md)

> [!div class="nextstepaction"]
> [Добавление элементов управления на карту](map-add-controls.md)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)
