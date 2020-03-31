---
title: Показать результаты поиска на карте Карты Microsoft Azure
description: В этой статье вы узнаете, как выполнить поисковый запрос с помощью Microsoft Azure Maps Web SDK и отобразить результаты на карте.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e82a1daee381c7bad19c83fa735d0028bef2010e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371395"
---
# <a name="show-search-results-on-the-map"></a>Отображение результатов поиска на карте

В этой статье объясняется, как выполнить поиск расположения и вывести результаты поиска на карту.

Существует два способа найти нужное место. Во-первых, можно использовать модуль службы для поискового запроса. Другой способ — сделать запрос на поиск в [API поиска Azure Maps Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) через [API Fetch API.](https://fetch.spec.whatwg.org/) Оба способа описаны ниже.

## <a name="make-a-search-request-via-service-module"></a>Поисковой запрос через модуль службы

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте (модуль службы)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>отображения результатов поиска на карте (служебный модуль)</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок строит объект карты и устанавливает механизм проверки подлинности для использования токена доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает `TokenCredential` для проверки подлинности запросы HTTP на Azure Maps с помощью маркера доступа. Затем он `TokenCredential` передает `atlas.service.MapsURL.newPipeline()` и создает экземпляр [трубопровода.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) `searchURL` представляет собой URL-адрес к операциям [поиска](https://docs.microsoft.com/rest/api/maps/search) службы Azure Maps.

В третьем блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), к которому добавляются результаты поиска. В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков.  Затем создается слой символа. Источник данных добавляется в слой символов, который затем добавляется на карту.

Четвертый блок кода использует метод [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) в [модуле обслуживания.](how-to-use-services-module.md) Это позволяет выполнять бесплатный поиск текста формы через [Get Search Fuzzy остальные API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) для поиска точки интереса. Получать запросы в API поиска нечетких может обрабатывать любую комбинацию нечетких входов. Затем с помощью метода `geojson.getFeatures()` из ответа извлекается коллекция функций GeoJSON и добавляется к источнику данных, после чего данные автоматически отображаются на карте с помощью слоя символов.

Последний блок кода регулирует границы камеры для карты с помощью свойства [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) карты.

Запрос поиска, источник данных, слой символов и границы камеры находятся внутри [слушателя события](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) на карте. Мы хотим, чтобы результаты отображались после полной загрузки карты.


## <a name="make-a-search-request-via-fetch-api"></a>Сделать запрос на поиск через API Fetch

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>отображения результатов поиска на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. Он устанавливает механизм проверки подлинности для использования токена доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает URL-адрес для запроса поиска. Он также создает два массива для хранения границ и контактов для результатов поиска.

Третий блок кода использует [API Fetch.](https://fetch.spec.whatwg.org/) [API Fetch](https://fetch.spec.whatwg.org/) используется для запроса в [API поиска Azure Maps Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) для поиска достижимых точек. API поиска нечетких соответствий может обрабатывать любую комбинацию нечетких входных данных. Затем он обрабатывает и разбирает поисковый ответ и добавляет контакты результата в массив searchPins.

Четвертый блок кода создает объект источника данных с помощью класса [DataSource.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) В коде мы добавляем результаты поиска к исходному объекту. В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков. Затем создается слой символа. Источник данных добавляется в слой символов, который затем добавляется на карту.

Последний блок кода создает объект [BoundingBox.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) Он использует массив результатов, а затем регулирует границы камеры для карты с помощью [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)карты. Затем он отображает контакты результата.

Запрос поиска, источник данных, слой символов и границы камеры устанавливаются в [пределах слушателя события](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты, чтобы гарантировать, что результаты отображаются после полной загрузки карты.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Рекомендации по использованию службы поиска Azure Maps](how-to-use-best-practices-for-search.md)

Дополнительные сведения о **поиске нечетких соответствий**:

> [!div class="nextstepaction"]
> [API поиска нечетких соответствий службы "Карты Azure"](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Карта](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Получение сведений на основе координат](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Отображение направлений от точки А до точки Б](map-route.md)
