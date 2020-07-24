---
title: Отображение сведений о координатах на карте | Microsoft Azure Maps
description: Узнайте, как отобразить сведения об адресе на карте, когда пользователь выбирает координаты.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 02ffbd9352b97fd0968aee89b0e8f41e3bc30713
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87130415"
---
# <a name="get-information-from-a-coordinate"></a>Получение сведений из координат

В этой статье показано, как осуществлять обратный поиск адреса, который показывает адрес расположения выбранного всплывающего окна.

Существует два способа обратного поиска адреса. Во-первых — запрос к [API обратного поиска адреса Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) через модуль службы. Другой способ — использовать [API получения](https://fetch.spec.whatwg.org/), чтобы выполнить запрос к [API обратного поиска адреса Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), чтобы найти адрес. Оба способа описаны ниже.

## <a name="make-a-reverse-search-request-via-service-module"></a>Обратный поиск через модуль службы

<iframe height='500' scrolling='no' title='Получение сведений на основе координат (модуль службы)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>получения сведений на основе координат (модуль службы)</a> для службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок создает объект карты и настраивает механизм проверки подлинности на использование маркера доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает `TokenCredential` для проверки подлинности HTTP-запросов к Azure Maps с маркером доступа. Затем он передает `TokenCredential` в `atlas.service.MapsURL.newPipeline()` и создает экземпляр [конвейера](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest). `searchURL` представляет собой URL-адрес к операциям [поиска](https://docs.microsoft.com/rest/api/maps/search) службы Azure Maps.

В третьем блоке кода стиль курсора мыши меняется на указатель и создается объект [всплывающего окна](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

В четвертом блоке кода добавляется [прослушиватель событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) для щелчков мышью. При срабатывании создается поисковой запрос с координатами щелкнутого расположения. Затем он использует метод [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) для запроса к [API обратного поиска адреса](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) для получения адреса координат. Затем извлекается коллекция функций GeoJSON с помощью метода `geojson.getFeatures()` из ответа.

Пятый блок кода настраивает HTML-содержимое всплывающего окна для вывода адреса для нажатой координаты.

Изменения курсора, объект всплывающего окна и события щелчка создаются в [прослушивателе событий загрузки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Эта структура кода обеспечивает полную загрузку карт перед получением сведений о координатах.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Обратный поиск через API получения

Щелкните карту, чтобы выполнить обратный запрос геокода для этого расположения с помощью fetch.

<iframe height='500' scrolling='no' title='Получение сведений из координат' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>получения сведений на основе координат</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок кода конструирует объект карты и устанавливает механизм проверки подлинности для использования маркера доступа. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода стиль курсора мыши меняется на указатель. Он создает экземпляр объекта [всплывающего окна](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

В третьем блоке кода добавляется прослушиватель событий для щелчков мышью. Когда пользователь щелкает мышью, используется [API получения](https://fetch.spec.whatwg.org/) для отправки запроса к [API обратного поиска адреса Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), чтобы запросить координаты соответствующего адреса. Для успешного ответа он собирает адрес для щелкнутого расположения. Он определяет содержимое и расположение всплывающего окна с помощью функции [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) класса всплывающего окна.

Изменения курсора, объект всплывающего окна и события щелчка создаются в [прослушивателе событий загрузки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Эта структура кода обеспечивает полную загрузку карт перед получением сведений о координатах.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Рекомендации по использованию службы поиска Azure Maps](how-to-use-best-practices-for-search.md)

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Схема](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Отображение направлений от точки А до точки Б](./map-route.md)

> [!div class="nextstepaction"]
> [Отображение трафика](./map-show-traffic.md)
