---
title: Отображение сведений о координатах в службе "Карты Azure" | Документация Майкрософт
description: Как отобразить сведения об адресе на карте, когда пользователь выбирает координаты
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: df0966569a753d5000414451a2b69f1e69449b2c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638662"
---
# <a name="get-information-from-a-coordinate"></a>Получение сведений из координат

В этой статье показано, как осуществлять обратный поиск адреса, который показывает адрес расположения выбранного всплывающего окна.

Существует два способа обратного поиска адреса. Во-первых — запрос к [API обратного поиска адреса Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) через модуль службы. Другой способ — использовать [API выборки](https://fetch.spec.whatwg.org/) , чтобы выполнить запрос к [Azure Maps API поиска обратных адресов](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) для поиска адреса. Оба способа описаны ниже.

## <a name="make-a-reverse-search-request-via-service-module"></a>Обратный поиск через модуль службы

<iframe height='500' scrolling='no' title='Получение сведений на основе координат (модуль службы)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>получения сведений на основе координат (модуль службы)</a> для службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок кода конструирует объект Map и устанавливает механизм проверки подлинности для использования маркера доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает `TokenCredential` для проверки подлинности HTTP-запросов, Azure Maps с маркером доступа. Затем он передает `TokenCredential` в `atlas.service.MapsURL.newPipeline()` и создает экземпляр [конвейера](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . `searchURL` представляет собой URL-адрес к операциям [поиска](https://docs.microsoft.com/rest/api/maps/search) службы Azure Maps.

Третий блок кода обновляет стиль курсора мыши на указатель и создает объект [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) . См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

Четвертый блок кода добавляет [прослушиватель событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)щелчка мыши. При запуске создается поисковый запрос с координатами нажатой точки. Затем он использует метод [жетсеарчаддрессреверсе](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) модуля службы, чтобы запросить [обратный API адреса поиска Get](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) для адреса координат. После этого коллекция функций геоjson из ответа извлекается с помощью `geojson.getFeatures()` метода.

Пятый блок кода настраивает HTML-содержимое всплывающего окна для вывода адреса отклика для нажатой координаты.

Изменение курсора, объект всплывающего окна и событие щелчка создаются в [прослушивателе событий загрузки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Так карта будет полностью загружаться до получения информации о координатах.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Создание запроса на обратный поиск с помощью API FETCH

Щелкните карту, чтобы выполнить обратный запрос геокода для этого расположения с помощью Fetch.

<iframe height='500' scrolling='no' title='Получение сведений из координат' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>получения сведений на основе координат</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок кода конструирует объект Map и устанавливает механизм проверки подлинности для использования маркера доступа. См. инструкции по [созданию карты](./map-create.md).

В третьем блоке кода стиль курсора мыши меняется на указатель и объект [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

В третьем блоке кода добавляется прослушиватель событий для щелчков мышью. При щелчке мышью он использует [API выборки](https://fetch.spec.whatwg.org/) для запроса [api-интерфейса Azure Maps обратный поиск адреса](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) для адреса щелчков по координатам. При получении ответа фиксируется адрес щелкнутого расположения. Также в коде определяется содержимое и расположение всплывающего окна с помощью функции [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) класса Popup.

Изменение курсора, объект всплывающего окна и событие щелчка создаются в [прослушивателе событий загрузки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Так карта будет полностью загружаться до получения информации о координатах.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Отображение направлений от точки А до точки Б](./map-route.md)

> [!div class="nextstepaction"]
> [Отображение трафика](./map-show-traffic.md)
