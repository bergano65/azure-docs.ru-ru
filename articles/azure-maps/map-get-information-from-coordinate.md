---
title: Отображение сведений о координатах в службе "Карты Azure" | Документация Майкрософт
description: Как отобразить сведения об адресе на карте, когда пользователь выбирает координаты
author: jingjing-z
ms.author: jinzh
ms.date: 09/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: bf44437f4d0b60a5d56c2be29418b7132346da2e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732300"
---
# <a name="get-information-from-a-coordinate"></a>Получение сведений на основе координат

В этой статье показано, как осуществлять обратный поиск адреса, который показывает адрес расположения выбранного всплывающего окна.

Существует два способа обратного поиска адреса. Во-первых — запрос к [API обратного поиска адреса Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) через модуль службы. Во-вторых — запрос [XMLHttpRequest](https://xhr.spec.whatwg.org/) к API для поиска адреса. Оба способа описаны ниже.

## <a name="make-a-reverse-search-request-via-service-module"></a>Обратный поиск через модуль службы

<iframe height='500' scrolling='no' title='Получение сведений на основе координат (модуль службы)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>получения сведений на основе координат (модуль службы)</a> для службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Строка во втором блоке кода создает экземпляр клиента службы.

Во третьем блоке кода стиль курсора мыши меняется на указатель.

В четвертом блоке кода создается всплывающее окно с помощью [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

В последнем блоке кода [добавляется прослушиватель событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) для щелчка мышью. При нажатии кнопки мыши создается поисковой запрос с координатами щелкнутого расположения. Затем используется конечная точка [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) карты, чтобы запросить координаты из адреса.

При получении ответа фиксируется адрес щелкнутого расположения. Также в коде определяется содержимое и расположение всплывающего окна с помощью функции [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) класса Popup.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Обратный поиск через XMLHttpRequest

<iframe height='500' scrolling='no' title='Получение сведений на основе координат' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>получения сведений на основе координат</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода стиль курсора мыши меняется на указатель.

В третьем блоке кода создается всплывающее окно с помощью [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

В последнем блоке кода добавляется прослушиватель событий для щелчка мышью. Когда пользователь щелкнет кнопкой мыши, [XMLHttpRequest](https://xhr.spec.whatwg.org/) отправляется в [API обратного поиска адресов для службы "Карты Azure"](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). При получении ответа фиксируется адрес щелкнутого расположения. Также в коде определяется содержимое и расположение всплывающего окна с помощью функции [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) класса Popup.

## <a name="next-steps"></a>Дополнительная информация

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
