---
title: Добавление слоя изображений в Azure Maps | Документация Майкрософт
description: Добавление слоя изображения в веб-пакет SDK Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fadaaf7c64b11a6d6d94c68234f8288d1b3f8d07
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480494"
---
# <a name="add-an-image-layer-to-a-map"></a>Добавление слоя изображений на карту

В этой статье показано, как можно накладывать изображение на фиксированный набор координат на карте. Существует множество сценариев, в которых выполняется наложение изображения на карту. Ниже приведены несколько примеров типов изображений, которые часто накладываются на карты.

* Изображения, снятые с дронов.
* Планы этажей зданий.
* Исторические или другие специализированные изображения для карт.
* Чертежи строительных площадок.
* Изображения с метеорологических радаров.

> [!TIP]
> Класс [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) — это быстрый и простой способ наложения изображений на карту. Тем не менее, если изображение велико, браузеру будет трудно загрузить его. В этом случае следует разбить изображение на фрагменты и загрузить их на карту в виде объекта [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Слой изображений поддерживает следующие форматы изображений:

- JPEG
- PNG
- BMP
- GIF (без анимации)

## <a name="add-an-image-layer"></a>Добавление слоя изображений

В следующем коде накладывается изображение [схемы Ньюарке New Джерси с 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) на карте. [Имажелайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) создается путем передачи URL-адреса изображению и координат для четырех углов в формате `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Ниже приведен полный пример выполнения кода описанной выше функциональности.

<br/>

<iframe height='500' scrolling='no' title='Простой слой изображений' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/eQodRo/'>простого слоя карты</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в репозитории <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Импорт наземного наложения KML

В этом примере показано, как выполнить наземное наложение KML как слоя изображений на карте. Наложение заземления КМЛ предоставляет координаты Севера, Юг, Восток и Запад, а также поворот по часовой стрелке, в то время как слой изображения ожидает координаты для каждого угла изображения. Наземное наложение KML в этом примере является изображением Шартрского собора, взятым на [Викимедии](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

В следующем коде используется статическая функция `getCoordinatesFromEdges` класса [имажелайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) для вычисления четырех углов изображения из сведений о севере, Южной, Восток, западе и повороте из наложения КМЛ.

<br/>

<iframe height='500' scrolling='no' title='Наземное наложение KML как слоя изображений' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>наземное наложение KML как слоя изображений</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в репозитории <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Настройка слоя изображений

Слой изображений имеет множество вариантов стилизации, которые можно опробовать.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя изображений' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода с <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>параметрами слоя изображений</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Класс ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Интерфейс ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление слоя фрагментов](./map-add-tile-layer.md)