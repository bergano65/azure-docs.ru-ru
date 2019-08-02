---
title: Отображение данных дорожного движения с помощью службы "Карты Azure" | Документация Майкрософт
description: Как отобразить данные дорожного движения на карте Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 55bfc434082b2d5b7de193e969fc34f710657cdb
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638637"
---
# <a name="show-traffic-on-the-map"></a>Отображение данных дорожного движения на карте

В этой статье показано, как отобразить на карте данные дорожного движения и сведения о проблемах на дороге.

## <a name="understand-the-code"></a>Изучение кода

<iframe height='456' scrolling='no' title='Отображение данных дорожного движения на карте' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>отображения данных дорожного движения на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](map-create.md).

Во втором блоке кода используется функция [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) в рамках функции карты [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Она позволяет отобразить на карте данные дорожного движения и сведения о проблемах на дороге.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Страница с примерами кода](https://aka.ms/AzureMapsSamples)

Расширьте возможности пользователей:

> [!div class="nextstepaction"]
> [Взаимодействие карты с событиями мыши](./map-events.md)

> [!div class="nextstepaction"]
> [Создание карты](./map-accessibility.md)
