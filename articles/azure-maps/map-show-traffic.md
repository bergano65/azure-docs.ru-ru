---
title: Отображение данных дорожного движения на карте | Microsoft Azure Maps
description: Узнайте, как добавить данные трафика в карты. Сведения о потоковых данных и о том, как использовать веб-пакет SDK Azure Maps для добавления данных о инцидентах и потоковых данных в карты.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 063fbd2ad4f2f5d427fd2cb39b8ce9b231eba374
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036431"
---
# <a name="show-traffic-on-the-map"></a>Отображение данных дорожного движения на карте

В Azure Maps доступны два типа данных трафика:

- Данные по происшествиям — это данные в виде точек и линий, которые обозначают, помимо прочего, строительные работы, закрытие дорог и аварии.
- Данные по потоку — метрики потока уличного движения. Данные по потоку уличного движения часто используются для раскрашивания дорог. Цвет зависит от того, насколько скорость движения потока меньше максимальной разрешенной скорости, или от другой метрики. Данные по потоку уличного движения в Azure Maps имеют три различные метрики:
    - `relative` — определяется относительно скорости свободного потока на дороге;
    - `absolute` — абсолютная скорость всех транспортных средств на дороге;
    - `relative-delay` — участки, скорость движения на которых меньше средней ожидаемой задержки.

Ниже приведен код для отображения данных дорожного движения на карте.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Ниже приведен полный и выполняемый пример этой функциональности.

<br/>

<iframe height='500' scrolling='no' title='Отображение данных дорожного движения на карте' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>отображения данных дорожного движения на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Параметры наложения сведений о дорожном движении

Представленное ниже средство позволяет переключаться между различными параметрами наложения сведений о дорожном движении, чтобы увидеть, как изменяется визуализация. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Параметры наложения сведений о дорожном движении" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Параметры наложения сведений о дорожном движении</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Схема](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Расширьте возможности пользователей:

> [!div class="nextstepaction"]
> [Взаимодействие карты с событиями мыши](map-events.md)

> [!div class="nextstepaction"]
> [Создание карты](map-accessibility.md)

> [!div class="nextstepaction"]
> [Страница с примерами кода](https://aka.ms/AzureMapsSamples)
