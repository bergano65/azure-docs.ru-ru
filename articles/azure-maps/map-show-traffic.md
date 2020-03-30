---
title: Отображение трафика на карте Карты Microsoft Azure
description: В этой статье вы узнаете, как отображать данные о трафике на карте с помощью Веб-SDK Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534820"
---
# <a name="show-traffic-on-the-map"></a>Отображение данных дорожного движения на карте

В Azure Maps имеется два типа данных о трафике:

- Данные об инцидентах - состоят из точечных и линейных данных для таких вещей, как строительство, закрытие дорог и аварии.
- Данные потока - обеспечивает метрики по потоку трафика на дорогах. Часто данные о транспортном потоке используются для окраски дорог. Цвета основаны на том, сколько трафика замедляет поток, по отношению к ограничению скорости, или другой метрики. Данные о транспортном потоке в Azure Maps имеют три различных метрики измерения:
    - `relative`- относительно скорости свободного потока дороги.
    - `absolute`- это абсолютная скорость всех транспортных средств на дороге.
    - `relative-delay`- отображает области, которые медленнее, чем средняя ожидаемая задержка.

В следующем коде показано, как отобразить данные о трафике на карте.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Ниже приводится полный выборка кода приведена вышеуказанной функциональности.

<br/>

<iframe height='500' scrolling='no' title='Отображение данных дорожного движения на карте' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>отображения данных дорожного движения на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Параметры наложения сведений о дорожном движении

Следующий инструмент позволяет переключаться между различными настройками наложения трафика, чтобы увидеть, как изменяется рендеринг. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Параметры наложения сведений о дорожном движении" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Ознакомьтесь с <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>вариантами наложения</a> на Pen Traffic по картам Azure ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Карта](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Расширьте возможности пользователей:

> [!div class="nextstepaction"]
> [Взаимодействие карты с событиями мыши](map-events.md)

> [!div class="nextstepaction"]
> [Создание карты](map-accessibility.md)

> [!div class="nextstepaction"]
> [Страница с примерами кода](https://aka.ms/AzureMapsSamples)
