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
ms.openlocfilehash: 49c86f3e6c654ecbfcd07809f42a1b038ca3f8ab
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911106"
---
# <a name="create-a-map"></a>Создание карты

В этой статье показаны способы, позволяющие создать и анимировать карту.  

## <a name="loading-a-map"></a>Загрузка схемы

Чтобы загрузить карту, создайте новый экземпляр [класса Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). При инициализации Map идентификатор элемента DIV для отрисовки Map и набор параметров, используемых при загрузке схемы. Если сведения о проверке подлинности по умолчанию не указаны в пространстве имен `atlas`, эти сведения необходимо указать в параметрах Map при загрузке схемы. Эта схема асинхронно загружает несколько ресурсов для повышения производительности. Таким образом, после создания экземпляра Map присоедините к карте событие `ready` или `load`, а затем добавьте дополнительный код, который взаимодействует с картой в этом обработчике событий. Событие `ready` срабатывает, как только на карте загружено достаточно ресурсов для взаимодействия с программным способом. Событие `load` срабатывает после полного завершения загрузки представления первоначальной таблицы. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Базовая Загрузка карт" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел " <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Базовая схема</a> перьевого отображения", Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> На одной странице можно загрузить несколько карт, и для каждого из них можно использовать одинаковые параметры проверки подлинности и языка.

## <a name="show-a-single-copy-of-the-world"></a>Отображение одной копии мира

Когда на широком экране отображается изображение, несколько копий мира отображаются по горизонтали. Это замечательно для большинства сценариев, но некоторые для некоторых приложений могут быть желательным только одной копии мира. Это можно сделать, задав для параметра Maps `renderWorldCopies` значение `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Рендерворлдкопиес = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Ознакомьтесь с пером <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>рендерворлдкопиес = false</a> , Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Управление камерой карт

Вы можете задать отображаемую область на карте с помощью камеры двумя способами. Можно задать параметры камеры, такие как центрирование и масштаб, при загрузке схемы или вызвать `setCamera` параметр в любое время после загрузки картой для программного обновления представления карт.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Настройка камеры

В следующем коде создается [объект Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) , а также устанавливаются параметры центрирования и масштабирования. Свойства карт, такие как центр и масштаб, являются частью [камераоптионс](/javascript/api/azure-maps-control/atlas.cameraoptions).

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

В следующем коде первый блок кода создает карту и задает стиль отображения, центр и значения масштаба. Во втором блоке кода для кнопки анимировать создается обработчик событий нажатия. При нажатии этой кнопки функция Сеткамера вызывается с некоторыми случайными значениями для [камераоптионс](/javascript/api/azure-maps-control/atlas.cameraoptions), [аниматионоптионс](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Просмотр анимации карты' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Просмотр анимации карты</a> для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Тестирование кода

Давайте рассмотрим подробнее приведенный выше пример. Вы можете редактировать код JavaScript на **вкладке JS** в левой части экрана и сразу видеть изменения в представлении карты на **вкладке "Результат"** справа. Кроме того, вы можете изменить код в CodePen с помощью кнопки **Редактировать в CodePen**.

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