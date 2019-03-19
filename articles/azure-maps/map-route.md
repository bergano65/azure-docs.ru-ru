---
title: Отображение маршрутов с помощью службы "Карты Azure" | Документация Майкрософт
description: В этой статье объясняется, как отобразить маршрут между двумя расположениями на карте в JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 786880c5fa919fce5ed60d011211e6d7348f7260
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570068"
---
# <a name="show-directions-from-a-to-b"></a>Отображение направлений от точки А до точки Б

В этой статье объясняется, как выполнить запрос маршрута и отобразить маршрут на карте.

Это можно сделать двумя способами. Во-первых — запрос к [API маршрута Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) через модуль службы. Второй способ — использование [выборки API](https://fetch.spec.whatwg.org/) сделать запрос поиска для [API маршрутов Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Оба способа описаны ниже.

## <a name="query-the-route-via-service-module"></a>Запрос маршрута с помощью модуля службы

<iframe height='500' scrolling='no' title='Отображение направлений от точки А до точки Б на карте (модуль службы)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Отображение направлений от точки А до точки Б на карте (модуль службы)</a> для службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первым блоком кода создает объект карты и задает способ проверки подлинности, использовать ключ подписки. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает **SubscriptionKeyCredentialPolicy** для проверки подлинности HTTP-запросов для службы карт Azure с помощью ключа подписки. **Atlas.service.MapsURL.newPipeline()** принимает **SubscriptionKeyCredential** политики и создает [конвейера](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) экземпляра. **RouteURL** представляет URL-адрес для службы карт Azure [маршрута](https://docs.microsoft.com/rest/api/maps/route) операций.

Третий блок кода создает и добавляет [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) объекта на карту.

Четвертый блок кода создает начала и окончания [точек](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) объектов и добавляет их в объекте источника данных.

Линия является [компонентом](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) класса LineString. [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) отвечает за отображение линейных объектов в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) в качестве линий на карте. Четвертый блок кода позволяет создать и добавить на карту слой линий. Ознакомьтесь со свойствами слоя линий в разделе о классе [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков. Пятый блок кода позволяет создать и добавить на карту слой символов.

Шестой блок кода запрашивает службу маршрутизации "карты Azure", входящий в состав из [модуль службы](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2). [CalculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) метод RouteURL используется для получения маршрута между начальной и конечной точек. Коллекция компонентов GeoJSON из ответа извлекается с помощью **geojson.getFeatures()** метод и добавляется к источнику данных. После этого ответ отображается в виде маршрута на карте. Дополнительные сведения о добавлении линии на карту см. [здесь](./map-add-shape.md#addALine).

Последний блок кода задает границы элемента карты, с помощью карты [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) свойство.

Запрос маршрута, источник данных, слой символов и линий, а также границы камеры создаются и задаются в [прослушивателе событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Таким образом, результаты отображаются после полной загрузки карты.

## <a name="query-the-route-via-fetch-api"></a>Запрос маршрута с помощью выборки API

<iframe height='500' scrolling='no' title='Отображение направлений от точки А до точки Б на карте' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Отображение направлений от точки А до точки Б на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первым блоком кода создает объект карты и задает способ проверки подлинности, использовать ключ подписки. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает и добавляет на карту объект [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest).

Третий блок кода создает точки начала и назначения для маршрута и добавляет их в источник данных. Инструкции по использованию [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) см. в разделе о [добавлении булавки на карту](map-add-pin.md).

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) отвечает за отображение линейных объектов в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) в качестве линий на карте. Четвертый блок кода позволяет создать и добавить на карту слой линий. Ознакомьтесь со свойствами слоя линий в разделе о классе [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков. Пятый блок кода позволяет создать и добавить на карту слой символов. Сведения о свойствах слоя символов см. в разделе [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Следующий блок кода создает точки `SouthWest` и `NorthEast` от точек начала и назначения и задает границы карты с помощью свойства карты [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-).

Последний блок кода использует [выборки API](https://fetch.spec.whatwg.org/) сделать запрос поиска для [API маршрутов Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Затем анализируется входящий ответ. И для успешного ответа он собирает сведения о широте и долготе для каждой точки маршрута и создает массив из строки, подключив эти моменты. Затем все эти линии добавляются в dataSource для отображения маршрута на карте. См. инструкции по [добавлению линий на карту](./map-add-shape.md#addALine).

Запрос маршрута, источник данных, слой символов и линий, а также границы камеры создаются и задаются в [прослушивателе событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Таким образом, результаты отображаются после полной загрузки карты.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Отображение данных дорожного движения на карте](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Взаимодействие с картой — события мыши](./map-events.md)
