---
title: Добавление слоя изображения в Azure Maps | Документация Майкрософт
description: Добавление слоя изображения в веб-пакет SDK Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 631a9e2d44b798404ee7567d3ccfed90628d2f8b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432863"
---
# <a name="add-an-image-layer-to-a-map"></a>Добавление слоя изображений на карту

В этой статье показано, как можно наложить изображение на фиксированный набор координат на карте. Ниже приведены некоторые примеры типов изображений, часто наложенных на карты:

* Образы, полученные из дроны
* Создание флурпланс
* Исторические или другие специализированные изображения карт
* Схемы сайтов заданий
* Лепестковые изображения погоды

> [!TIP]
> [Имажелайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) — это простой способ наложения изображения на карту. Обратите внимание, что обозреватели могут испытывать трудности при загрузке большого изображения. В этом случае рассмотрите возможность разбить изображение на плитки и загрузить их на карту как [тилелайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Слой изображений поддерживает следующие форматы изображений:

- JPEG
- PNG
- BMP
- GIF (без анимации)

## <a name="add-an-image-layer"></a>Добавление слоя изображений

Следующий код накладывает изображение на карту [Ньюарке, Нью-Джерси, с 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) на карте. [Имажелайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) создается путем передачи URL-адреса изображению и координат для четырех углов в формате `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

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

Ниже приведен полный пример выполнения кода предыдущего кода.

<br/>

<iframe height='500' scrolling='no' title='Простой слой изображений' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/eQodRo/'>простого слоя карты</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в репозитории <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Импорт наземного наложения KML

В следующем примере показано, как наложить сведения о наложения КМЛ на слой изображения на карте. Наложение заземления КМЛ предоставляет координаты Севера, Юг, Восток и Запад, а также поворот по часовой стрелке, в то время как слой изображения ожидает координаты для каждого угла изображения. Наложение заземления КМЛ в этом примере относится к Чартрес Каседрал и является источником из [некоммерческого](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

В следующем коде используется статическая функция `getCoordinatesFromEdges` класса [имажелайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) . Он вычисляет четыре угла изображения: от Севера, Южной, Восточной и Западной, а также от информации о повороте от наложения КМЛ.

<br/>

<iframe height='500' scrolling='no' title='Наземное наложение KML как слоя изображений' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>наземное наложение KML как слоя изображений</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в репозитории <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Настройка слоя изображений

На уровне изображения имеется много параметров стилей. Вот средство для пробных попыток.

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