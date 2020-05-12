---
title: Отображение результатов поиска на карте | Карты Microsoft Azure
description: В этой статье вы узнаете, как выполнить запрос поиска с помощью веб-пакета SDK Microsoft Azure Maps и отобразить результаты на карте.
author: Philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: da67c27f590e60c7ae4eecbe8e139c5519e39e31
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123961"
---
# <a name="show-search-results-on-the-map"></a>Отображение результатов поиска на карте

В этой статье объясняется, как выполнить поиск расположения и вывести результаты поиска на карту.

Существует два способа найти нужное место. Во-первых, можно использовать модуль службы для поискового запроса. Другой способ — сделать запрос поиска для [Azure Maps API нечетких поисков](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) через [API выборки](https://fetch.spec.whatwg.org/). Оба способа описаны ниже.

## <a name="make-a-search-request-via-service-module"></a>Поисковой запрос через модуль службы

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте (модуль службы)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>отображения результатов поиска на карте (служебный модуль)</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок конструирует объект Map и устанавливает механизм проверки подлинности для использования маркера доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает `TokenCredential` для проверки подлинности HTTP-запросов, Azure Maps с маркером доступа. Затем он передает `TokenCredential` в `atlas.service.MapsURL.newPipeline()` и создает экземпляр [конвейера](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . `searchURL` представляет собой URL-адрес к операциям [поиска](https://docs.microsoft.com/rest/api/maps/search) службы Azure Maps.

В третьем блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), к которому добавляются результаты поиска. В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков.  Затем создается слой символов. Источник данных добавляется в слой символов, который затем добавляется на карту.

Четвертый блок кода использует метод [сеарчфуззи](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) в [модуле службы](how-to-use-services-module.md). Он позволяет выполнять поиск в свободной форме с помощью [интерфейса API нечеткого поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) , чтобы найти интересующую вас точку. Запросы GET к API поиска с нечеткими данными могут работать с любым сочетанием нечетких входов. Затем с помощью метода `geojson.getFeatures()` из ответа извлекается коллекция функций GeoJSON и добавляется к источнику данных, после чего данные автоматически отображаются на карте с помощью слоя символов.

Последний блок кода корректирует границы камеры для схемы с помощью свойства [сеткамера](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) на карте.

Поисковый запрос, источник данных, уровень символов и границы камеры находятся внутри [прослушивателя событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) на карте. Мы хотим убедиться, что результаты отображаются после полной загрузки Map.


## <a name="make-a-search-request-via-fetch-api"></a>Создание поискового запроса через API выборки

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>отображения результатов поиска на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. Он задает механизм проверки подлинности для использования маркера доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает URL-адрес для выполнения запроса поиска. Он также создает два массива для хранения границ и ПИН-кодов для результатов поиска.

В третьем блоке кода используется [API выборки](https://fetch.spec.whatwg.org/). [API выборки](https://fetch.spec.whatwg.org/) используется для запроса [Azure Maps API нечетких поисков](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) для поиска интересующих точек. API поиска нечетких соответствий может обрабатывать любую комбинацию нечетких входных данных. Затем он обрабатывает и анализирует ответ поиска и добавляет результирующие контакты в массив Сеарчпинс.

Четвертый блок кода создает объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . В коде мы добавляем результаты поиска в исходный объект. В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков. Затем создается слой символов. Источник данных добавляется в слой символов, который затем добавляется на карту.

Последний блок кода создает объект [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) . Он использует массив результатов, а затем корректирует границы камеры для этой схемы с помощью [сеткамераа](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)на карте. Затем он отображает сигналы результата.

Поисковый запрос, источник данных, уровень символа и границы камеры задаются в [прослушивателе событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) сопоставлений, чтобы убедиться, что результаты отображаются после полной загрузки схемы.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Рекомендации по использованию службы поиска Azure Maps](how-to-use-best-practices-for-search.md)

Дополнительные сведения о **поиске нечетких соответствий**:

> [!div class="nextstepaction"]
> [API поиска нечетких соответствий службы "Карты Azure"](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Схема](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Получение сведений на основе координат](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Отображение направлений от точки А до точки Б](map-route.md)
