---
title: Отображение маршрутов с помощью службы "Карты Azure" | Документация Майкрософт
description: В этой статье объясняется, как отобразить маршрут между двумя расположениями на карте в JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: ed522779f5a86e38ee12a246cea9ac85d0379f9e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729066"
---
# <a name="show-directions-from-a-to-b"></a>Отображение направлений от точки А до точки Б

В этой статье объясняется, как выполнить запрос маршрута и отобразить маршрут на карте.

Это можно сделать двумя способами. Во-первых — запрос к [API маршрута Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) через модуль службы. Во-вторых  — запрос [XMLHttpRequest](https://xhr.spec.whatwg.org/) к API. Оба способа описаны ниже.

## <a name="query-the-route-via-service-module"></a>Запрос маршрута с помощью модуля службы

<iframe height='500' scrolling='no' title='Отображение направлений от точки А до точки Б на карте (модуль службы)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Отображение направлений от точки А до точки Б на карте (модуль службы)</a> для службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Строка во втором блоке кода создает экземпляр клиента службы.

Третий блок кода создает на карте экземпляр [слоя ломаной](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings).

В четвертом блоке кода создаются и добавляются на карту маркеры, представляющие собой начальную и конечную точку маршрута. Инструкции по использованию [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) см. в разделе о [добавлении булавки на карту](map-add-pin.md).

В следующем блоке кода используется функция [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) класса Map, чтобы задать координаты ограничивающего прямоугольника карты на основе начальной и конечной точек маршрута.

Шестой блок кода создает запрос маршрута.

Последний блок кода запрашивает службу маршрутизации Azure Maps с помощью метода [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections). Это позволяет получить маршрут между точками начала и назначения. Затем ответ преобразуется в формат GeoJSON с использованием метода [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes). Все эти линии добавляются на карту для отображения маршрута. Дополнительные сведения см. в разделе по [добавлению линий на карту](./map-add-shape.md#addALine).

## <a name="query-the-route-via-xmlhttprequest"></a>Запрос маршрута через XMLHttpRequest

<iframe height='500' scrolling='no' title='Отображение направлений от точки А до точки Б на карте' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Отображение направлений от точки А до точки Б на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создаются и добавляются на карту маркеры, представляющие начальную и конечную точку маршрута. Инструкции по использованию [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) см. в разделе о [добавлении булавки на карту](map-add-pin.md).

В третьем блоке кода используется функция [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) класса Map, чтобы задать координаты ограничивающего прямоугольника карты на основе начальной и конечной точек маршрута.

В четвертом блоке кода [XMLHttpRequest](https://xhr.spec.whatwg.org/) отправляется в [API маршрутов службы "Карты Azure"](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

В последнем блоке кода анализируется входящий ответ. При получении ответа собираются сведения о широте и долготе для каждой точки маршрута. Затем путем соединения каждой точки маршрута со следующей точкой создается массив линий. Все эти линии добавляются на карту для отображения маршрута. См. инструкции по [добавлению линий на карту](./map-add-shape.md#addALine).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Отображение данных дорожного движения на карте](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Взаимодействие с картой — события мыши](./map-events.md)
