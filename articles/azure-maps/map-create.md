---
title: Создание карты с помощью службы "Карты Azure" | Документация Майкрософт
description: Как создать карту с помощью веб-пакета SDK для Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 932c244ff41e757413a05cde019ee7ee1a82232d
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976123"
---
# <a name="create-a-map"></a>Создание карты

В этой статье показаны способы, позволяющие создать и анимировать карту.  

## <a name="loading-a-map"></a>Загрузка схемы

Чтобы загрузить карту, создайте новый экземпляр [класса Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). При инициализации Map идентификатор элемента DIV для отрисовки Map и набор параметров, используемых при загрузке схемы. Если в `atlas` пространстве имен не указаны сведения для проверки подлинности по умолчанию, эти сведения необходимо указать в параметрах Map при загрузке схемы. Эта схема асинхронно загружает несколько ресурсов для повышения производительности. Таким образом, после создания экземпляра Map присоедините `ready` событие или `load` к карте, а затем добавьте дополнительный код, который взаимодействует с картой в этом обработчике событий. `ready` Событие срабатывает, как только на карте загружено достаточно ресурсов для взаимодействия с программным способом. `load` Событие срабатывает после полного завершения загрузки представления первоначальной таблицы. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Базовая Загрузка карт" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел " <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Базовая схема</a> пера: Загрузка<a href='https://codepen.io/azuremaps'>@azuremaps</a>" с помощью Azure Maps () в <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> На одной странице можно загрузить несколько карт, и для каждого из них можно использовать одинаковые параметры проверки подлинности и языка.

## <a name="show-a-single-copy-of-the-world"></a>Отображение одной копии мира

Когда на широком экране отображается изображение, несколько копий мира отображаются по горизонтали. Это замечательно для большинства сценариев, но некоторые для некоторых приложений могут быть желательным только одной копии мира. Это можно сделать, задав для `renderWorldCopies` `false`параметра Map значение.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Рендерворлдкопиес = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. рендерворлдкопиес (перо) <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>= false</a> by<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Управление камерой карт

Вы можете задать отображаемую область на карте с помощью камеры двумя способами. Можно задать параметры камеры, такие как центрирование и масштаб, при загрузке схемы или вызвать параметр в `setCamera` любое время после загрузки картой, чтобы программно обновить представление Map.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Настройка камеры

В следующем коде создается [объект Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) , а также устанавливаются параметры центрирования и масштабирования. Свойства карт, такие как центр и масштаб, являются частью [камераоптионс](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Создание карты с помощью CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Создать карту через `CameraOptions` </a>от Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Настройка границ камеры

В следующем коде [объект Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) создается с помощью `new atlas.Map()`. Свойства карты, например `CameraBoundsOptions`, можно определить через функцию [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) класса Map. Свойства границ и заполнения задаются с помощью `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Создание карты с помощью CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Создание карты через `CameraBoundsOptions` </a>от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Просмотр анимации карты

В следующем коде первый блок кода создает карту и задает стиль отображения, центр и значения масштаба. Во втором блоке кода для кнопки анимировать создается обработчик событий нажатия. При нажатии этой кнопки функция Сеткамера вызывается с некоторыми случайными значениями для [камераоптионс](/javascript/api/azure-maps-control/atlas.cameraoptions), [аниматионоптионс](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Просмотр анимации карты' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Просмотр анимации карты</a> для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Тестирование кода

Давайте рассмотрим подробнее приведенный выше пример. Вы можете редактировать код JavaScript на **вкладке JS** в левой части экрана и сразу видеть изменения в представлении карты на **вкладке "Результат"** справа. Кроме того, вы можете изменить код в CodePen с помощью кнопки **Редактировать в CodePen**.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

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