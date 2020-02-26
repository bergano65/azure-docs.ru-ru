---
title: Отображение сведений об координатах на карте | Карты Microsoft Azure
description: Сведения о том, как отображать сведения об адресе на карте, когда пользователь выбирает координату.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1a6b3b4665e6141fb4c95508a8d8405268de6d19
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208528"
---
# <a name="get-information-from-a-coordinate"></a>Получение сведений из координат

В этой статье показано, как осуществлять обратный поиск адреса, который показывает адрес расположения выбранного всплывающего окна.

Существует два способа обратного поиска адреса. Во-первых — запрос к [API обратного поиска адреса Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) через модуль службы. Другой способ — использовать [API выборки](https://fetch.spec.whatwg.org/) , чтобы выполнить запрос к [Azure Maps API поиска обратных адресов](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) для поиска адреса. Оба способа описаны ниже.

## <a name="make-a-reverse-search-request-via-service-module"></a>Обратный поиск через модуль службы

<iframe height='500' scrolling='no' title='Получение сведений на основе координат (модуль службы)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>получения сведений на основе координат (модуль службы)</a> для службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок конструирует объект Map и устанавливает механизм проверки подлинности для использования маркера доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает `TokenCredential` для проверки подлинности HTTP-запросов, Azure Maps с маркером доступа. Затем он передает `TokenCredential` `atlas.service.MapsURL.newPipeline()` и создает экземпляр [конвейера](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . `searchURL` представляет собой URL-адрес к операциям [поиска](https://docs.microsoft.com/rest/api/maps/search) службы Azure Maps.

Третий блок кода обновляет стиль курсора мыши на указатель и создает объект [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) . См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

Четвертый блок кода добавляет [прослушиватель событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)щелчка мыши. При запуске создается поисковый запрос с координатами нажатой точки. Затем он использует метод [жетсеарчаддрессреверсе](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-), чтобы запросить [обратный API адреса поиска Get](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) для адреса координат. Затем коллекция функций геоjson извлекается с помощью метода `geojson.getFeatures()` из ответа.

Пятый блок кода настраивает HTML-содержимое всплывающего окна для вывода адреса отклика для нажатой координаты.

Изменения курсора, всплывающего объекта и события щелчка создаются в [прослушивателе событий загрузки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)на карте. Эта структура кода обеспечивает полную загрузку карт перед получением сведений о координатах.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Создание запроса на обратный поиск с помощью API FETCH

Щелкните карту, чтобы выполнить обратный запрос геокода для этого расположения с помощью Fetch.

<iframe height='500' scrolling='no' title='Получение сведений из координат' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>получения сведений на основе координат</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок кода конструирует объект Map и устанавливает механизм проверки подлинности для использования маркера доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода обновляет стиль курсора мыши на указатель. Он создает экземпляр [всплывающего](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) объекта. См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

В третьем блоке кода добавляется прослушиватель событий для щелчков мышью. При щелчке мышью он использует [API выборки](https://fetch.spec.whatwg.org/) , чтобы запросить [api-интерфейс Azure Maps обратный поиск адреса](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) для выбранного адреса координат. Для успешного ответа он собирает адрес для щелчка. Он определяет содержимое и расположение всплывающего окна с помощью функции [сетоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) класса Popup.

Изменения курсора, всплывающего объекта и события щелчка создаются в [прослушивателе событий загрузки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)на карте. Эта структура кода обеспечивает полную загрузку Map перед получением сведений о координатах.

## <a name="next-steps"></a>Следующие шаги

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
