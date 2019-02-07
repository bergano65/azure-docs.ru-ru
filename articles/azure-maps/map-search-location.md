---
title: Отображение результатов поиска с помощью службы "Карты Azure" | Документация Майкрософт
description: Как выполнять поисковой запрос с помощью службы Azure Maps и выводить результаты на карту в Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c68b4bdffde5f987fe07d50d76fa83e7bdfa5235
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755062"
---
# <a name="show-search-results-on-the-map"></a>Отображение результатов поиска на карте

В этой статье объясняется, как выполнить поиск расположения и вывести результаты поиска на карту.

Существует два способа найти нужное место. Во-первых, можно использовать модуль службы для поискового запроса. Во-вторых, можно сделать поисковой запрос через [XMLHttpRequest](https://xhr.spec.whatwg.org/) в [API поиска нечетких соответствий Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Оба способа описаны ниже.

## <a name="make-a-search-request-via-service-module"></a>Поисковой запрос через модуль службы

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте (модуль службы)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>отображения результатов поиска на карте (служебный модуль)</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В примере кода выше в его первом блоке создается объект карты и инициализируется клиентская служба. См. инструкции по [созданию карты](./map-create.md).

В [модуле службы](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1) во втором блоке кода используется метод [getSearchFuzzy](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchfuzzy-string--searchgetsearchfuzzyoptionalparams-). Он позволяет выполнять поиск текста произвольной формы с помощью [REST API поиска нечетких соответствий](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) для поиска точки интереса. API нечеткого поиска может обрабатывать любую комбинацию нечетких входных данных. Ответ службы поиска нечетких соответствий преобразуется в формате GeoJSON с помощью метода [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest). 

В третьем блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), к которому добавляются результаты поиска. В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков.  Источник данных добавляется на создаваемый слой символов, который затем добавляется на карту.

Последний блок кода корректирует границы камеры для карты с помощью свойства карты [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-).

Поисковый запрос, источник данных, слой символов и границы камеры создаются и задаются в [прослушивателе событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Таким образом, результаты отображаются после полной загрузки карты.


## <a name="make-a-search-request-via-xmlhttprequest"></a>Поисковой запрос через XMLHttpRequest

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>отображения результатов поиска на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода [XMLHttpRequest](https://xhr.spec.whatwg.org/) отправляется в [API поиска нечетких соответствий службы Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) для поиска точки интереса. API поиска нечетких соответствий может обрабатывать любую комбинацию нечетких входных данных. 

Третий блок кода выполняет синтаксический анализ ответа поиска и сохраняет результаты в массиве для вычисления границ. Затем он возвращает результаты поиска.

В четвертом блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), к которому добавляются результаты поиска. В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков. Источник данных добавляется на создаваемый слой символов, который затем добавляется на карту.

Последний блок кода создает объект [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) с помощью массива результатов и затем изменяет границы камеры для карты с помощью параметра карты [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Затем выполняется отрисовка результатов.

Поисковый запрос, источник данных, слой символов и границы камеры задаются в [прослушивателе событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Таким образом, результаты отображаются после полной загрузки карты.

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
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Отображение направлений от точки А до точки Б](./map-route.md)
