---
title: Обработка событий мыши с помощью службы "Карты Azure" | Документация Майкрософт
description: Как создать интерактивную карту Javascript с использованием событий карты
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: da6b183155de0fbc370751254a6842343d280874
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638967"
---
# <a name="interact-with-the-map---mouse-events"></a>Взаимодействие с картой — события мыши

В этой статье показано, как использовать свойство [событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [класса карты](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) для выделения событий на карте и на различных слоях карты. Здесь также показано, как использовать свойство событий класса карты для выделения событий при взаимодействии с маркером HTML.

## <a name="interact-with-the-map"></a>Взаимодействие с картой

<iframe height='600' scrolling='no' title='Взаимодействие с картой — события мыши' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Взаимодействие с картой — события мыши</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

Поработайте с указанной выше картой и посмотрите, как соответствующие события мыши выделяются в разделе справа. Вы можете щелкнуть **вкладку JS**, чтобы просмотреть и изменить код JavaScript. Кроме того, можно нажать кнопку **редактирования в CodePen** и изменить код.

## <a name="interact-with-map-layers"></a>Взаимодействие со слоями карты

<iframe height='600' scrolling='no' title='Взаимодействие с картой – события слоя' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Взаимодействие с картой – события слоя</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

Приведенный выше код выделяет название событий, которые запускаются при взаимодействии со слоем символов. Слои символов, пузырьков, линий и многоугольников поддерживают одинаковый набор событий. Слой мозаики не поддерживает ни одно из этих событий.

## <a name="interact-with-html-marker"></a>Взаимодействие с маркером HTML

<iframe height='500' scrolling='no' title='Взаимодействие с картой – события маркера HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Взаимодействие с картой – события маркера HTML</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

Приведенный выше код добавляет события карты Javascript в маркер HTML. Он также выделяет название событий, которые запускаются при взаимодействии с маркером HTML.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Использование модуля Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Страница с примерами кода](https://aka.ms/AzureMapsSamples)
