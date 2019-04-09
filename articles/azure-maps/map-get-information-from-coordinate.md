---
title: Отображение сведений о координатах в службе "Карты Azure" | Документация Майкрософт
description: Как отобразить сведения об адресе на карте, когда пользователь выбирает координаты
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 50f906a9d8a0dc19f5eb47bef4cb68f4703f020f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256062"
---
# <a name="get-information-from-a-coordinate"></a>Получение сведений на основе координат

В этой статье показано, как осуществлять обратный поиск адреса, который показывает адрес расположения выбранного всплывающего окна.

Существует два способа обратного поиска адреса. Во-первых — запрос к [API обратного поиска адреса Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) через модуль службы. Другой способ — использовать [выборки API](https://fetch.spec.whatwg.org/) сделать запрос к [Maps обратный адрес API службы поиска Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) найти адрес. Оба способа описаны ниже.

## <a name="make-a-reverse-search-request-via-service-module"></a>Обратный поиск через модуль службы

<iframe height='500' scrolling='no' title='Получение сведений на основе координат (модуль службы)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>получения сведений на основе координат (модуль службы)</a> для службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первым блоком кода создает объект карты и задает способ проверки подлинности, использовать ключ подписки. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает `SubscriptionKeyCredentialPolicy` для проверки подлинности HTTP-запросов для службы карт Azure с помощью ключа подписки. Затем `atlas.service.MapsURL.newPipeline()` принимает `SubscriptionKeyCredential` политики и создает [конвейера](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) экземпляра. `searchURL` Представляет URL-адрес для службы карт Azure [поиска](https://docs.microsoft.com/rest/api/maps/search) операций.

Третий блок кода, обновляет стиль курсор мыши на указатель и создает [всплывающее окно](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) объекта. См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

Четвертый блок кода добавляет щелчка кнопкой мыши [прослушиватель событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). При активации, он создает запрос поиска с координаты точки, которую щелкнул пользователь. Затем он использует модуль службы [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) метод для запроса [получить API обратного поиска адреса](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) для адреса координат. Коллекция компонентов GeoJSON из ответа извлекается с помощью `geojson.getFeatures()` метод.

Пятый блок кода устанавливает содержимое всплывающего окна HTML для отображения адреса ответа для выбранной координат положения.

Изменение курсора, объект всплывающего окна и событие щелчка создаются в [прослушивателе событий загрузки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Так карта будет полностью загружаться до получения информации о координатах.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Сделать запрос обратного поиска с помощью выборки API

Щелкните карту, чтобы сделать запрос обратного геокода к этому расположению, использование инструкции fetch.

<iframe height='500' scrolling='no' title='Получение сведений на основе координат' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>получения сведений на основе координат</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первым блоком кода создает объект карты и задает способ проверки подлинности, использовать ключ подписки. См. инструкции по [созданию карты](./map-create.md).

В третьем блоке кода стиль курсора мыши меняется на указатель и объект [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

В третьем блоке кода добавляется прослушиватель событий для щелчков мышью. При нажатии кнопки мыши, он использует [выборки API](https://fetch.spec.whatwg.org/) запрос [Maps обратный адрес API службы поиска Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) для адреса координаты, которую щелкнул пользователь. При получении ответа фиксируется адрес щелкнутого расположения. Также в коде определяется содержимое и расположение всплывающего окна с помощью функции [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) класса Popup.

Изменение курсора, объект всплывающего окна и событие щелчка создаются в [прослушивателе событий загрузки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Так карта будет полностью загружаться до получения информации о координатах.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Сопоставление](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Всплывающее окно](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Отображение направлений от точки А до точки Б](./map-route.md)

> [!div class="nextstepaction"]
> [Отображение трафика](./map-show-traffic.md)
