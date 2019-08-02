---
title: Добавление слоя фрагментов карты в Azure Maps | Документация Майкрософт
description: Сведения о добавлении слоя фрагментов карты на карту Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e288e03b9e2c02ba963595f192dea7225c6d5762
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638981"
---
# <a name="add-a-tile-layer-to-a-map"></a>Добавление слоя фрагментов на карту

Из этой статьи вы узнаете, как наложить слой фрагментов на карту. Слои фрагментов позволяют накладывать изображения поверх фрагментов карты в Azure Maps. Дополнительные сведения о системе фрагментов Azure Maps см. в статье [Уровни увеличения и параметры сетки](zoom-levels-and-tile-grid.md).

Слой фрагментов загружается в виде фрагментов с сервера. Эти изображения можно предварительно визуализировать и хранить (как и любое другое изображение) на сервере с помощью соглашения об именовании, которое слой фрагмента может распознать. Кроме того, можно воспользоваться динамической службой, которая создает изображения в режиме реального времени. Класс [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) в Azure Maps поддерживает три разных соглашения об именовании службы фрагментов. 

* X, Y, нотация увеличения. В зависимости от уровня увеличения, x — это столбец, а y — позиция строки фрагмента в сетке фрагментов.
* Нотация Quadkey. Сочетание данных x, y и увеличения в одном строковом значении, которое является уникальным идентификатором для фрагмента.
* Ограничивающий прямоугольник. Координаты ограничивающего прямоугольника позволяют указать образ в формате `{west},{south},{east},{north}`. Часто используется [веб-службами сопоставления (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Класс [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) — отличный способ визуализировать большие объемы данных на карте. С его помощью можно не только создать слой фрагментов из образа, но и отобразить векторные данные в виде слоя фрагментов. Отображение векторных данных в виде слоя фрагментов позволяет элементу управления картой загрузить фрагменты, размер файлов которых может быть значительно меньше, чем у векторных данных, которые они представляют. Эта техника часто используется, когда нужно преобразовать для просмотра огромное количество строк данных на карте.

URL-адрес фрагмента, передаваемый в слой фрагментов, должен быть URL-адресом с http/https к ресурсу TileJSON или шаблоном URL-адреса фрагмента, который использует следующие параметры: 

* `{x}` — позиция X фрагмента. Также нужны `{y}` и `{z}`.
* `{y}` — позиция Y фрагмента. Также нужны `{x}` и `{z}`.
* `{z}` — уровень увеличения фрагмента. Также нужны `{x}` и `{y}`.
* `{quadkey}` — идентификатор quadkey на основе соглашения об именовании системы фрагментов Bing Maps.
* `{bbox-epsg-3857}` — строка ограничивающего прямоугольника в формате `{west},{south},{east},{north}` в системе пространственных ссылок 3857 EPSG.
* `{subdomain}` — заполнитель, вместо которого будут добавлены значения поддомена, если они указаны.

## <a name="add-a-tile-layer"></a>Добавление слоя фрагментов

 В этом примере показано, как создать слой фрагментов, который указывает на набор фрагментов, использующих систему фрагментов с x, y и увеличением. Источником этого слоя фрагментов является наложение радара погоды из [лаборатории окружающей среды Университета штата Айова](https://mesonet.agron.iastate.edu/ogc/).

<br/>

<iframe height='500' scrolling='no' title='Слой фрагментов с X, Y и Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Слой фрагментов с X, Y и Z</a> в Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) создается путем передачи отформатированного URL-адреса в службу фрагмента, размера фрагмента и уровня непрозрачности, чтобы сделать его полупрозрачным. Кроме того, при добавлении слоя фрагментов на карту, он добавляется под слой `labels`, чтобы метки по-прежнему были четко видны.

## <a name="customize-a-tile-layer"></a>Настройка слоя фрагментов

Слой фрагментов имеет несколько параметров стиля, которые можно опробовать.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя фрагментов' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Параметры слоя фрагментов</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление слоя изображения](./map-add-image-layer.md)
