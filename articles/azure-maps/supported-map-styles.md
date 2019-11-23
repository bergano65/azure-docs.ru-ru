---
title: Поддерживаемые стили карт в службе Azure Maps | Документация Майкрософт
description: Стили карт, поддерживаемые в службе Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 5368aec04eb0a57654adf5b0d5914282a979dcfd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325401"
---
# <a name="azure-maps-supported-map-styles"></a>Стили карт, поддерживаемые в службе Azure Maps
Служба Azure Maps поддерживает несколько различных встроенных стилей карт, как описано ниже.

## <a name="road"></a>дорога
Карта **дороги** — это стандартная карта, которая отображает дороги, природные и искусственные объекты, а также ярлыки для этих объектов.

![дорога](./media/supported-map-styles/road.png)

**Применимые API:**
* [Изображение карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Фрагмент карты](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control
* Android map control

## <a name="blank-and-blank_accessible"></a>blank and blank_accessible

The **blank** and **blank_accessible** map styles provide a blank canvas on which to visualize data on. The **blank_accessible** style will continue to provide screen reader updates with location details of where the map is located, even though the base map is not displayed.

> [!Note]
> In the web SDK you can change the background color of the map by setting the CSS `background-color` style of map DIV element.

**Применимые API:**
* Web SDK map control

## <a name="satellite"></a>спутник 
Стиль **спутник** представляет собой комбинацию спутниковых и аэроснимков.

![спутник](./media/supported-map-styles/satellite.png)

**Применимые API:**
* [Фрагмент спутниковой карты](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK map control
* Android map control

## <a name="satellite_road_labels"></a>satellite_road_labels
Этот стиль карты представляет собой гибрид дорог и ярлыков, наложенных поверх спутниковых и аэроснимков.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Применимые API:**
* Web SDK map control
* Android map control

## <a name="grayscale_dark"></a>grayscale_dark
**Темно-серый** — это темная версия стиля дорожной карты.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Применимые API:**
* [Изображение карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Фрагмент карты](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control 
* Android map control


## <a name="grayscale_light"></a>grayscale_light
**grayscale light** is a light version of the road map style.

![grayscale light](./media/supported-map-styles/grayscale_light.png)

**Применимые API:**
* Web SDK map control
* Android map control


## <a name="night"></a>ночь
**Ночь** — это темная версия стиля дорожной карты с цветными дорогами и символами.

![ночь](./media/supported-map-styles/night.png)

**Применимые API:**
* Web SDK map control
* Android map control

## <a name="road_shaded_relief"></a>road_shaded_relief
**road shaded relief** — это основной стиль Azure Maps, дополненный рельефами Земли.

![shaded relief](./media/supported-map-styles/shaded-relief.png)

**Применимые API:**
* [Фрагмент карты](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control
* Android map control


## <a name="next-steps"></a>Дальнейшие действия

Learn about how to set a map style in Azure Maps:

> [!div class="nextstepaction"]
> [Выбор стиля карты](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
