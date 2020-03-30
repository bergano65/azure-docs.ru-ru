---
title: Добавление слоя изображения на карту Карты Microsoft Azure
description: В этой статье вы узнаете о том, как наложить изображение на карту с помощью веб-SDK Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209721"
---
# <a name="add-an-image-layer-to-a-map"></a>Добавление слоя изображений на карту

В этой статье показано, как наложить изображение на фиксированный набор координат. Вот несколько примеров различных типов изображений, которые могут быть наложены на карты:

* Изображения, снятые с беспилотников
* Планы зданий
* Исторические или другие специализированные изображения карт
* Чертежи рабочих мест
* Изображения метеорологического радара

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) — это простой способ наложения изображения на карту. Обратите внимание, что браузеры могут испытывать трудности с загрузкой большого изображения. В этом случае рассмотрите возможность разбить изображение на плитку и загрузить его на карту в качестве [плитки.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

Слой изображения поддерживает следующие форматы изображения:

- JPEG
- PNG
- BMP
- GIF (без анимации)

## <a name="add-an-image-layer"></a>Добавление слоя изображений

Следующий код накладывает изображение [карты Ньюарка, Нью-Джерси, с 1922 года](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) на карте. [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) создается путем передачи URL на изображение и координаты для четырех углов в формате. `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`

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

Вот полный пример кода, приведенного в эксплуатации, который был приведен в истории предыдущего кода.

<br/>

<iframe height='500' scrolling='no' title='Простой слой изображений' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/eQodRo/'>простого слоя карты</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в репозитории <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Импорт файла KML в качестве наземного наложения

В этом примере показано, как добавить информацию о наложении земли KML в качестве слоя изображения на карте. Наземные накладки KML обеспечивают северные, южные, восточные и западные координаты, а также вращение по часовой стрелке. Но слой изображения ожидает координаты для каждого угла изображения. Накладка земли KML в этом образце предназначена для собора Шартра, и она получена из [Викимедиа](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Код использует статическую `getCoordinatesFromEdges` функцию из класса [ImageLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) Он вычисляет четыре угла изображения, используя северную, южную, восточную, западную и вращение наземной накладки KML.

<br/>

<iframe height='500' scrolling='no' title='Наземное наложение KML как слоя изображений' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>наземное наложение KML как слоя изображений</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в репозитории <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Настройка слоя изображений

Слой изображения имеет много вариантов укладки. Вот инструмент, чтобы попробовать их.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя изображений' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода с <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>параметрами слоя изображений</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Класс ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Интерфейс ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление слоя фрагментов](./map-add-tile-layer.md)