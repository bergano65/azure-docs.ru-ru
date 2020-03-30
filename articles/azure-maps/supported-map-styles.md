---
title: Поддерживаемые стили карт Карты Microsoft Azure
description: В этой статье вы узнаете о различных стилях визуализации карт, поддерживаемых Картами Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2eafe3c16a89723d55ec52fde785e9ec69e45e0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334040"
---
# <a name="azure-maps-supported-map-styles"></a>Стили карт, поддерживаемые в службе Azure Maps
Служба Azure Maps поддерживает несколько различных встроенных стилей карт, как описано ниже.

## <a name="road"></a>road
Карта **дороги** — это стандартная карта, которая отображает дороги, природные и искусственные объекты, а также ярлыки для этих объектов.

![Стиль дорожной карты](./media/supported-map-styles/road.png)

**Применимые API:**
* [Изображение карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Карта плитки](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Управление картой веб-SDK
* Управление картой Android

## <a name="blank-and-blank_accessible"></a>пустой и blank_accessible

Стили **пустой** и **blank_accessible** карты обеспечивают пустой холст, на котором можно визуализировать данные. Стиль blank_accessible будет **по-прежнему** предоставлять обновления чтения с экраном с деталями местоположения карты, даже если базовая карта не отображается.

> [!Note]
> В веб-SDK можно изменить цвет фона карты, `background-color` установив стиль CSS элемента DIV карты.

**Применимые API:**
* Управление картой веб-SDK

## <a name="satellite"></a>спутник 
Стиль **спутник** представляет собой комбинацию спутниковых и аэроснимков.

![спутниковая плитка карта стиль](./media/supported-map-styles/satellite.png)

**Применимые API:**
* [Фрагмент спутниковой карты](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Управление картой веб-SDK
* Управление картой Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Этот стиль карты представляет собой гибрид дорог и ярлыков, наложенных поверх спутниковых и аэроснимков.

![satellite_road_labels стиль карты](./media/supported-map-styles/satellite-road-labels.png)

**Применимые API:**
* Управление картой веб-SDK
* Управление картой Android

## <a name="grayscale_dark"></a>grayscale_dark
**Темно-серый** — это темная версия стиля дорожной карты.

![стиль карты gray_scale](./media/supported-map-styles/grayscale-dark.png)

**Применимые API:**
* [Изображение карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Карта плитки](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Управление картой веб-SDK 
* Управление картой Android


## <a name="grayscale_light"></a>grayscale_light
**серый свет** является легкой версией стиля дорожной карты.

![серый стиль световой карты](./media/supported-map-styles/grayscale-light.png)

**Применимые API:**
* Управление картой веб-SDK
* Управление картой Android


## <a name="night"></a>ночь
**Ночь** — это темная версия стиля дорожной карты с цветными дорогами и символами.

![ночной картой стиль](./media/supported-map-styles/night.png)

**Применимые API:**
* Управление картой веб-SDK
* Управление картой Android

## <a name="road_shaded_relief"></a>road_shaded_relief
**road shaded relief** — это основной стиль Azure Maps, дополненный рельефами Земли.

![затененный стиль рельефной карты](./media/supported-map-styles/shaded-relief.png)

**Применимые API:**
* [Карта плитки](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Управление картой веб-SDK
* Управление картой Android

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** — это стиль темной карты с более высоким контрастом, чем другие стили.

![высокий контраст темной карты стиль](./media/supported-map-styles/high-contrast-dark.png)

**Применимые API:**
* Управление картой веб-SDK

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о том, как настроить стиль карты в Azure Maps:

> [!div class="nextstepaction"]
> [Выбор стиля карты](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
