---
title: Добавить слой плитки на карту Карты Microsoft Azure
description: В этой статье вы узнаете, как наложить слой плитки на карте, используя Microsoft Azure Maps Web SDK. Слои плитки позволяют визуализировать изображения на карте.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988606"
---
# <a name="add-a-tile-layer-to-a-map"></a>Добавление слоя фрагментов на карту

В этой статье показано, как наложить слой плитки на карте. Слои фрагментов позволяют накладывать изображения поверх фрагментов карты в Azure Maps. Для получения дополнительной информации о системе [Zoom levels and tile grid](zoom-levels-and-tile-grid.md)плитки Azure Maps см.

Слой плитки загружается в плитки с сервера. Эти изображения могут быть предварительно переданы или динамически рендеризированы. Предварительно отрисованные изображения хранятся, как и любое другое изображение на сервере, используя конвенцию именования, которую понимает слой плитки. Динамически отображаемые изображения используют службу для загрузки изображений в режиме реального времени. Существует три различных конвенции по именованию услуг плитки, поддерживаемые классом Azure Maps [TileLayer:](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 

* X, Y, Увеличить обозначение - X является столбец, Y строка положение плитки в сетке плитки, и увеличить обозначение значение на основе уровня зума.
* Обозначение quadkey - Комбинирует информацию x, y и масштабирует сякратить в одно значение строки. Это значение строки становится уникальным идентификатором для одной плитки.
* Bounding Box - Укажите изображение в формате `{west},{south},{east},{north}`координат коробки Bounding: . Этот формат обычно используется [Web Mapping Services (WMS).](https://www.opengeospatial.org/standards/wms)

> [!TIP]
> Класс [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) — отличный способ визуализировать большие объемы данных на карте. Слой плитки может образовываться не только из изображения, но и впереносить данные, которые также могут отображаться как слой плитки. Отображая векторные данные в виде слоя плитки, элемент управления картами должен загружать только плитки, которые меньше по размеру файла, чем векторные данные, которые они представляют. Этот метод обычно используется для визуализации миллионов строк данных на карте.

URL-адрес плитки, передаваемый в слой плитки, должен быть http или https URL на ресурс TileJSON или шаблон URL-адреса плитки, который использует следующие параметры: 

* `{x}` — позиция X фрагмента. Также нужны `{y}` и `{z}`.
* `{y}` — позиция Y фрагмента. Также нужны `{x}` и `{z}`.
* `{z}` — уровень увеличения фрагмента. Также нужны `{x}` и `{y}`.
* `{quadkey}` — идентификатор quadkey на основе соглашения об именовании системы фрагментов Bing Maps.
* `{bbox-epsg-3857}` — строка ограничивающего прямоугольника в формате `{west},{south},{east},{north}` в системе пространственных ссылок 3857 EPSG.
* `{subdomain}`- заполнитель для значений субдоменов, если `subdomain` указано, будет добавлено.

## <a name="add-a-tile-layer"></a>Добавление слоя фрагментов

 В этом примере показано, как создать слой плитки, который указывает на набор плиток. В этом примере используется система плитки x, y, зума. Источником этого слоя фрагментов является наложение радара погоды из [лаборатории окружающей среды Университета штата Айова](https://mesonet.agron.iastate.edu/ogc/). При просмотре радиолокационных данных, в идеале пользователи будут четко видеть метки городов, как они перемещаются по карте. Такое поведение можно реализовать путем вставки слоя плитки под слоем. `labels`

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Ниже приводится полный выборка кода приведена вышеуказанной функциональности.

<br/>

<iframe height='500' scrolling='no' title='Слой фрагментов с X, Y и Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Слой фрагментов с X, Y и Z</a> в Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Настройка слоя фрагментов

Класс слоя плитки имеет много вариантов укладки. которые можно опробовать с помощью представленного ниже средства.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя фрагментов' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Параметры слоя фрагментов</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление слоя изображений](./map-add-image-layer.md)
