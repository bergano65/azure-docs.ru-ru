---
title: Отображение результатов поиска с помощью службы "Карты Azure" | Документация Майкрософт
description: Как выполнять поисковой запрос с помощью службы Azure Maps и выводить результаты на карту в Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 78ffa42bcf57b7163afc13b2550abdbae240ef00
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729247"
---
# <a name="show-search-results-on-the-map"></a>Отображение результатов поиска на карте

В этой статье объясняется, как выполнить поиск расположения и вывести результаты поиска на карту.

Существует два способа найти нужное место. Во-первых, можно использовать модуль службы для поискового запроса. Во-вторых, можно сделать поисковой запрос через [XMLHttpRequest](https://xhr.spec.whatwg.org/) в [API поиска нечетких соответствий Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Оба способа описаны ниже.

## <a name="make-a-search-request-via-service-module"></a>Поисковой запрос через модуль службы

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте (модуль службы)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>отображения результатов поиска на карте (служебный модуль)</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода создается объект карты и инициализируется клиентская служба. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода использует [API поиска нечетких соответствий службы Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) для поиска точек интереса. API нечеткого поиска может обрабатывать любую комбинацию нечетких входных данных. Ответ службы поиска нечетких соответствий преобразуется в формате GeoJSON с помощью метода [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse). Маркеры добавлены на карту, чтобы указывать на точки интереса на карте.

Последний блок кода корректирует границы камеры для карты с использованием свойства [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest).

## <a name="make-a-search-request-via-xmlhttprequest"></a>Поисковой запрос через XMLHttpRequest

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>отображения результатов поиска на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода добавляет к карте уровень результатов поиска. На уровне результатов поиска отображаются результаты в виде булавок на карте. Булавки добавляются с помощью [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

В третьем блоке кода [XMLHttpRequest](https://xhr.spec.whatwg.org/) отправляется в [API поиска нечетких соответствий службы Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) для поиска точки интереса. API нечеткого поиска может обрабатывать любую комбинацию нечетких входных данных.

Последний блок кода анализирует ответ и корректирует границы камеры для карты с помощью [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) для отрисовки булавок.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о **поиске нечетких соответствий**:

> [!div class="nextstepaction"]
> [API поиска нечетких соответствий службы "Карты Azure"](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Получение сведений на основе координат](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Отображение направлений от точки А до точки Б](./map-route.md)
