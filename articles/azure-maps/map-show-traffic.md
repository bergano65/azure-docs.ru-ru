---
title: Отображение данных дорожного движения с помощью службы "Карты Azure" | Документация Майкрософт
description: Сведения о том, как отображать данные трафика в веб-пакете SDK Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 145e2246703441a08868c8aae311573e95d4de42
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976437"
---
# <a name="show-traffic-on-the-map"></a>Отображение данных дорожного движения на карте

В Azure Maps доступны два типа данных трафика:

- Данные об инцидентах — это данные на основе точек и строк, такие как создание, замыкание в командировки и всякое.
- Данные потока — предоставляет метрики потока трафика на дорогих. Данные потока трафика часто используются для цвета дорог в зависимости от того, какой объем трафика замедляет работу потока относительно предельной скорости или какой-либо другой метрики. Данные потока трафика в Azure Maps имеют три различные метрики измерения:
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

## <a name="traffic-overlay-options"></a>Параметры оверлея трафика

Следующий инструмент позволяет переключаться между различными параметрами наложения трафика, чтобы увидеть, как изменяется визуализация. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Параметры оверлея трафика" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Параметры наложения</a> перьевого трафика<a href='https://codepen.io/azuremaps'>@azuremaps</a>с помощью Azure Maps () в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map);

> [!div class="nextstepaction"]
> [траффикоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Расширьте возможности пользователей:

> [!div class="nextstepaction"]
> [Взаимодействие карты с событиями мыши](map-events.md)

> [!div class="nextstepaction"]
> [Создание карты](map-accessibility.md)

> [!div class="nextstepaction"]
> [Страница с примерами кода](https://aka.ms/AzureMapsSamples)
