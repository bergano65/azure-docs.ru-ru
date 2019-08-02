---
title: Добавление слоя изображений в Azure Maps | Документация Майкрософт
description: Сведения о добавлении слоя изображений на карту JavaScript
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7ea0f37e307196af4b27fd3f8fb1aa0d42443dfa
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638733"
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

## <a name="add-an-image-layer"></a>Добавление слоя изображений

В этом примере показано, как наложить изображение [карты Ньюарка, Нью-Джерси, (1922 г.)](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) на карту.

<br/>

<iframe height='500' scrolling='no' title='Простой слой изображений' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/eQodRo/'>простого слоя карты</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в репозитории <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) создается с помощью передачи URL-адреса изображения и координат четырех углов в формате `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

## <a name="import-a-kml-ground-overlay"></a>Импорт наземного наложения KML

В этом примере показано, как выполнить наземное наложение KML как слоя изображений на карте. Наземное наложение KML обеспечивает координаты с Севера, Юга, Востока и Запада и вращение против часовой стрелки, где в качестве слоя изображений ожидаются координаты для каждого угла изображения. Наземное наложение KML в этом примере является изображением Шартрского собора, взятым на [Викимедии](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

<br/>

<iframe height='500' scrolling='no' title='Наземное наложение KML как слоя изображений' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Изучите фрагмент кода <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>наземное наложение KML как слоя изображений</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в репозитории <a href='https://codepen.io'>CodePen</a>.
</iframe>

В приведенном выше коде используется статическая функция `getCoordinatesFromEdges` класса [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) для вычисления четырех углов изображения с Севера, Юга, Востока, Запада и информации о вращении из наземного наложения KML.


## <a name="customize-an-image-layer"></a>Настройка слоя изображений

Слой изображений имеет множество вариантов стилизации, которые можно опробовать с помощью представленного ниже средства.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя изображений' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода с <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>параметрами слоя изображений</a>, опубликованный для Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Класс ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Интерфейс ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление слоя фрагментов](./map-add-tile-layer.md)