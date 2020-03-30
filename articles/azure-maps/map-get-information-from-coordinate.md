---
title: Показать информацию о координате на карте Карты Microsoft Azure
description: Узнайте, как отобразить информацию об адресе на карте, когда пользователь выбирает координат.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 174bdc496e52a6ac8f2a2d631db92e0f21a819be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371433"
---
# <a name="get-information-from-a-coordinate"></a>Получение сведений из координат

В этой статье показано, как осуществлять обратный поиск адреса, который показывает адрес расположения выбранного всплывающего окна.

Существует два способа обратного поиска адреса. Во-первых — запрос к [API обратного поиска адреса Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) через модуль службы. Другой способ — использовать [API Fetch,](https://fetch.spec.whatwg.org/) чтобы сделать запрос в [API поиска обратных адресов Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) для поиска адреса. Оба способа описаны ниже.

## <a name="make-a-reverse-search-request-via-service-module"></a>Обратный поиск через модуль службы

<iframe height='500' scrolling='no' title='Получение сведений на основе координат (модуль службы)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>получения сведений на основе координат (модуль службы)</a> для службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок строит объект карты и устанавливает механизм проверки подлинности для использования токена доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода `TokenCredential` создает для проверки подлинности запросы HTTP на Azure Maps с помощью маркера доступа. Затем он `TokenCredential` передает `atlas.service.MapsURL.newPipeline()` и создает экземпляр [трубопровода.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) `searchURL` представляет собой URL-адрес к операциям [поиска](https://docs.microsoft.com/rest/api/maps/search) службы Azure Maps.

Третий блок кода обновляет стиль курсора мыши до указателя и создает [всплывающий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) объект. См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

Четвертый блок кода добавляет [слушателя события](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)мыши щелчка мыши. При срабатывании он создает поисковый запрос с координатами нажатой точки. Затем он использует метод [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)для запроса [АИП](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) Адреса поиска для адреса координат. Затем с помощью ответа с помощью метода `geojson.getFeatures()` извлекается коллекция функций GeoJSON.

Пятый блок кода настраивает всплывающее содержимое HTML для отображения адреса ответа для позиции нажатой координат.

Изменение курсора, всплывающих объектов и события щелчка созданы в [слушателе события загрузки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)карты. Эта структура кода обеспечивает полную загрузку карты перед извлечением информации о координатах.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Сделать обратный запрос поиска через Fetch API

Нажмите на карту, чтобы сделать обратный запрос геокода для этого местоположения с помощью fetch.

<iframe height='500' scrolling='no' title='Получение сведений из координат' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>получения сведений на основе координат</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок кода строит объект карты и устанавливает механизм проверки подлинности для использования токена доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода обновляет стиль курсора мыши до указателя. Он мгновенно [всплывающих](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) объектов. См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

В третьем блоке кода добавляется прослушиватель событий для щелчков мышью. При щелчке мыши он использует [API Fetch](https://fetch.spec.whatwg.org/) для запроса [API поиска обратных](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) адресов Azure Maps для адреса нажатых координат. Для успешного ответа он собирает адрес для нажатого местоположения. Он определяет всплывающее содержимое и положение с помощью функции [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) всплывающих класса.

Изменение курсора, всплывающих объектов и события щелчка созданы в [слушателе события загрузки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)карты. Эта структура кода обеспечивает полную загрузку карты перед извлечением информации о координатах.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Рекомендации по использованию службы поиска Azure Maps](how-to-use-best-practices-for-search.md)

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Карта](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Контекстное меню](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Отображение направлений от точки А до точки Б](./map-route.md)

> [!div class="nextstepaction"]
> [Отображение данных дорожного движения](./map-show-traffic.md)
