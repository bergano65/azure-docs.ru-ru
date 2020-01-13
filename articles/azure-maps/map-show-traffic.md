---
title: Отображение трафика на карте | Карты Microsoft Azure
description: В этой статье вы узнаете, как отображать данные трафика на карте с помощью веб-пакета SDK для карт Microsoft Azure.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8370b71bfa980dbd099ade59a3a19633dfd5dc72
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910896"
---
# <a name="show-traffic-on-the-map"></a>Отображение данных дорожного движения на карте

В Azure Maps доступны два типа данных трафика:

- Данные об инцидентах — это данные на основе точек и строк, такие как создание, замыкание в командировки и всякое.
- Данные потока — предоставляет метрики потока трафика на дорогих. Данные потока трафика часто используются для цвета дорог в зависимости от того, какой объем трафика замедляет работу потока относительно предельной скорости или какой-либо другой метрики. Данные потока трафика в Azure Maps имеют три различные метрики измерения:
    - `relative` — относительно скорости произвольного потока пути.
    - `absolute` — это абсолютная скорость всех автомобилей в дороге.
    - `relative-delay` — отображает области, которые выполняются медленнее среднего ожидаемой задержки.

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

## <a name="traffic-overlay-options"></a>Параметры оверлея трафика

Следующий инструмент позволяет переключаться между различными параметрами наложения трафика, чтобы увидеть, как изменяется визуализация. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Параметры оверлея трафика" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Дополнительные сведения см. в разделе <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Параметры наложения</a> перьевого трафика Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
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
