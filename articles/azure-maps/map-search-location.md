---
title: Отображение результатов поиска с помощью службы "Карты Azure" | Документация Майкрософт
description: Как выполнить запрос поиска с Azure Maps затем отобразить результаты в веб-пакете SDK для Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7c4c78e1d21754d42391a3762e9f7ed199a7376b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975969"
---
# <a name="show-search-results-on-the-map"></a>Отображение результатов поиска на карте

В этой статье объясняется, как выполнить поиск расположения и вывести результаты поиска на карту.

Существует два способа найти нужное место. Во-первых, можно использовать модуль службы для поискового запроса. Другой способ — сделать запрос поиска для [Azure Maps API нечетких поисков](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) через [API выборки](https://fetch.spec.whatwg.org/). Оба способа описаны ниже.

## <a name="make-a-search-request-via-service-module"></a>Поисковой запрос через модуль службы

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте (модуль службы)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>отображения результатов поиска на карте (служебный модуль)</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок кода конструирует объект Map и устанавливает механизм проверки подлинности для использования маркера доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает `TokenCredential` для проверки подлинности HTTP-запросов, Azure Maps с маркером доступа. Затем он передает `TokenCredential` в `atlas.service.MapsURL.newPipeline()` и создает экземпляр [конвейера](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . `searchURL` представляет собой URL-адрес к операциям [поиска](https://docs.microsoft.com/rest/api/maps/search) службы Azure Maps.

В третьем блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), к которому добавляются результаты поиска. В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков.  Источник данных добавляется на создаваемый слой символов, который затем добавляется на карту.

Четвертый блок кода использует метод [сеарчфуззи](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) в [модуле службы](how-to-use-services-module.md). Он позволяет выполнять поиск в свободной форме с помощью [интерфейса API нечеткого поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) , чтобы найти интересующую вас точку. API-интерфейс нечеткого поиска может работать с любым сочетанием нечетких входов. Затем с помощью метода `geojson.getFeatures()` из ответа извлекается коллекция функций GeoJSON и добавляется к источнику данных, после чего данные автоматически отображаются на карте с помощью слоя символов.

Последний блок кода корректирует границы камеры для карты с помощью свойства карты [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-).

Запрос поиска, источник данных и уровень символов и границы камеры создаются и задаются в [прослушивателе событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) готовности к отображению, чтобы убедиться, что результаты отображаются после полной загрузки карт.


## <a name="make-a-search-request-via-fetch-api"></a>Создание поискового запроса через API выборки

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>отображения результатов поиска на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде первый блок кода конструирует объект Map и устанавливает механизм проверки подлинности для использования маркера доступа. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает URL-адрес для выполнения запроса поиска. Он также создает два массива для хранения границ и ПИН-кодов для результатов поиска.

Третий блок кода использует [API выборки](https://fetch.spec.whatwg.org/) , чтобы выполнить запрос на [Azure Maps API нечеткого поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) для поиска интересующих точек. API поиска нечетких соответствий может обрабатывать любую комбинацию нечетких входных данных. Затем он обрабатывает и анализирует ответ поиска и добавляет результирующие контакты в массив Сеарчпинс.

В четвертом блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), к которому добавляются результаты поиска. В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков. Источник данных добавляется на создаваемый слой символов, который затем добавляется на карту.

Последний блок кода создает объект [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) с помощью массива результатов и затем изменяет границы камеры для карты с помощью параметра карты [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Затем он отображает сигналы результата.

Поисковый запрос, источник данных, слой символов и границы камеры задаются в [прослушивателе событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) карты. Таким образом, результаты отображаются после полной загрузки карты.

## <a name="next-steps"></a>Следующие шаги

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
