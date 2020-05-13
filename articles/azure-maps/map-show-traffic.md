---
title: Отображение трафика на карте | Карты Microsoft Azure
description: В этой статье вы узнаете, как отображать данные трафика на карте с помощью веб-пакета SDK для карт Microsoft Azure.
author: Philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9c17c3cc22d478d81ed3c2b2ae9f61c173aad6cd
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123927"
---
# <a name="show-traffic-on-the-map"></a>Отображение данных дорожного движения на карте

В Azure Maps доступны два типа данных трафика:

- Данные об инцидентах — это данные на основе точек и строк, такие как создание, замыкание в командировки и всякое.
- Данные потока — предоставляет метрики потока трафика на дорогих. Часто данные потока трафика используются для цветовой дороги. Цвета основываются на том, какой объем трафика замедляет работу потока, относительно предельной скорости или другой метрики. Данные потока трафика в Azure Maps имеют три различные метрики измерения:
    - `relative`— относительно скорости произвольного потока пути.
    - `absolute`— Это абсолютная скорость всех транспортных средств в дороге.
    - `relative-delay`— Отображает области, которые выполняются медленнее среднего ожидаемой задержки.

В следующем коде показано, как отобразить данные трафика на карте.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Ниже приведен полный пример выполнения кода описанной выше функциональности.

<br/>

<iframe height='500' scrolling='no' title='Отображение данных дорожного движения на карте' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>отображения данных дорожного движения на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Параметры наложения сведений о дорожном движении

Следующий инструмент позволяет переключаться между различными параметрами наложения трафика, чтобы увидеть, как изменяется визуализация. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Параметры наложения сведений о дорожном движении" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Параметры наложения</a> перьевого трафика с помощью Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Схема](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [траффикоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Расширьте возможности пользователей:

> [!div class="nextstepaction"]
> [Взаимодействие карты с событиями мыши](map-events.md)

> [!div class="nextstepaction"]
> [Создание карты](map-accessibility.md)

> [!div class="nextstepaction"]
> [Страница с примерами кода](https://aka.ms/AzureMapsSamples)
