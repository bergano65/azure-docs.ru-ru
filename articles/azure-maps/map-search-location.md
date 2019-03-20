---
title: Отображение результатов поиска с помощью службы "Карты Azure" | Документация Майкрософт
description: Как выполнять поисковой запрос с помощью службы Azure Maps и выводить результаты на карту в Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8ae6c8a20a05df723d3f6b394e0639f218896a85
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845143"
---
# <a name="show-search-results-on-the-map"></a>Отображение результатов поиска на карте

В этой статье объясняется, как выполнить поиск расположения и вывести результаты поиска на карту.

Существует два способа найти нужное место. Во-первых, можно использовать модуль службы для поискового запроса. Другой способ — чтобы сделать запрос поиска на [API Azure Maps нечеткого поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) через [выборки API](https://fetch.spec.whatwg.org/). Оба способа описаны ниже.

## <a name="make-a-search-request-via-service-module"></a>Поисковой запрос через модуль службы

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте (модуль службы)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>отображения результатов поиска на карте (служебный модуль)</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первым блоком кода создает объект карты и задает способ проверки подлинности, использовать ключ подписки. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает **SubscriptionKeyCredentialPolicy** для проверки подлинности HTTP-запросов для службы карт Azure с помощью ключа подписки. Затем **atlas.service.MapsURL.newPipeline()** принимает **SubscriptionKeyCredential** политики и создает [конвейера](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) экземпляра. **SearchURL** представляет URL-адрес для службы карт Azure [поиска](https://docs.microsoft.com/rest/api/maps/search) операций.

В третьем блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), к которому добавляются результаты поиска. В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков.  Источник данных добавляется на создаваемый слой символов, который затем добавляется на карту.

Четвертый блок кода использует [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) метод в [модуль службы](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2). Он позволяет выполнять поиск текста произвольной формы с помощью [Get поиска нечетких соответствий rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) для поиска достопримечательностей. Get поиска нечетких соответствий API может обрабатывать любую комбинацию входов нечетких соответствий. Коллекция компонентов GeoJSON из ответа извлекается с помощью **geojson.getFeatures()** метод и добавляется к источнику данных, который автоматически приводит к данных, отображаемую на карте через уровень символ.

Последний блок кода корректирует границы камеры для карты с помощью свойства карты [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-).

Поиск запроса, создаются источник данных и символ слоя и границы камеры и набора в схеме готовы [прослушиватель событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) чтобы убедиться, что результаты отображаются после полной загрузки карты.


## <a name="make-a-search-request-via-fetch-api"></a>Сделать запрос поиска с помощью выборки API

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>отображения результатов поиска на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первым блоком кода создает объект карты и задает способ проверки подлинности, использовать ключ подписки. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает URL-адрес для создания запроса для поиска. Он также создает два массива для хранения границ и ПИН-кодов для результатов поиска.

Использует третий блок кода [выборки API](https://fetch.spec.whatwg.org/) сделать запрос к [API Azure Maps нечеткого поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) для поиска точек интерес. API поиска нечетких соответствий может обрабатывать любую комбинацию нечетких входных данных. Затем обрабатывает и анализирует ответ поиска и добавляет результат закрепления searchPins массива.

В четвертом блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), к которому добавляются результаты поиска. В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков. Источник данных добавляется на создаваемый слой символов, который затем добавляется на карту.

Последний блок кода создает объект [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) с помощью массива результатов и затем изменяет границы камеры для карты с помощью параметра карты [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Затем она визуализирует результат закрепления.

Поисковый запрос, источник данных, слой символов и границы камеры задаются в [прослушивателе событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Таким образом, результаты отображаются после полной загрузки карты.

## <a name="next-steps"></a>Дальнейшие действия

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
