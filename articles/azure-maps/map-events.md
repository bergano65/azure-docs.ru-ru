---
title: Обработка событий мыши с помощью службы "Карты Azure" | Документация Майкрософт
description: Как создать интерактивную карту веб-пакета SDK с помощью событий Map
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 37a3fc3178fe5caeacedfd355a6065ee189a5890
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976529"
---
# <a name="interact-with-the-map---mouse-events"></a>Взаимодействие с картой — события мыши

В этой статье показано, как использовать свойство [события класса Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) для выделения событий на карте и на разных слоях на карте. Здесь также показано, как использовать свойство событий класса карты для выделения событий при взаимодействии с маркером HTML.

## <a name="interact-with-the-map"></a>Взаимодействие с картой

Посмотрите на карту ниже и просмотрите соответствующие события мыши, выделенные справа. Вы можете щелкнуть **вкладку JS**, чтобы просмотреть и изменить код JavaScript. Кроме того, можно нажать кнопку **редактирования в CodePen** и изменить код.

<br/>

<iframe height='600' scrolling='no' title='Взаимодействие с картой — события мыши' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Взаимодействие с картой — события мыши</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Взаимодействие со слоями карты

В следующем коде выделены имена событий, которые создаются при взаимодействии с уровнем символов. На уровне символов, пузырьковых, линий и многоугольников поддерживается один и тот же набор событий. Уровни тепловой карты и мозаичных уровней не поддерживают ни одно из этих событий.

<br/>

<iframe height='600' scrolling='no' title='Взаимодействие с картой – события слоя' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Взаимодействие с картой – события слоя</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Взаимодействие с маркером HTML

Следующий код добавляет события сопоставлений JavaScript в HTML-маркер. Он также выделяет название событий, которые запускаются при взаимодействии с маркером HTML.

<br/>

<iframe height='500' scrolling='no' title='Взаимодействие с картой – события маркера HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Взаимодействие с картой – события маркера HTML</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Следующие шаги

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Использование модуля Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)
