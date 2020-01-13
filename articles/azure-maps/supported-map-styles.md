---
title: Поддерживаемые стили карт | Карты Microsoft Azure
description: В этой статье вы узнаете о различных стилях рендеринга карт, поддерживаемых картами Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 92e48486777d82589a56074790dd709c5d525859
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910642"
---
# <a name="azure-maps-supported-map-styles"></a>Стили карт, поддерживаемые в службе Azure Maps
Служба Azure Maps поддерживает несколько различных встроенных стилей карт, как описано ниже.

## <a name="road"></a>road
Карта **дороги** — это стандартная карта, которая отображает дороги, природные и искусственные объекты, а также ярлыки для этих объектов.

![стиль схемы дороги](./media/supported-map-styles/road.png)

**Применимые API:**
* [Изображение карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Фрагмент карты](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android

## <a name="blank-and-blank_accessible"></a>пусто и blank_accessible

**Пустые** и **blank_accessible** стили карт предоставляют пустой холст для визуализации данных. Стиль **blank_accessible** продолжит предоставлять обновления средства чтения с экрана с подробными сведениями о расположении, где расположена схема, даже если базовая схема не отображается.

> [!Note]
> В веб-пакете SDK можно изменить цвет фона на карте, установив стиль CSS `background-color` элемента Map DIV.

**Применимые API:**
* Элемент управления картой веб-пакета SDK

## <a name="satellite"></a>спутник 
Стиль **спутник** представляет собой комбинацию спутниковых и аэроснимков.

![стиль карты вспомогательных плиток](./media/supported-map-styles/satellite.png)

**Применимые API:**
* [Фрагмент спутниковой карты](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Этот стиль карты представляет собой гибрид дорог и ярлыков, наложенных поверх спутниковых и аэроснимков.

![satellite_road_labels стиль схемы](./media/supported-map-styles/satellite_road_labels.png)

**Применимые API:**
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android

## <a name="grayscale_dark"></a>grayscale_dark
**Темно-серый** — это темная версия стиля дорожной карты.

![gray_scale стиль схемы](./media/supported-map-styles/grayscale_dark.png)

**Применимые API:**
* [Изображение карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Фрагмент карты](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Элемент управления картой веб-пакета SDK 
* Элемент управления картой Android


## <a name="grayscale_light"></a>grayscale_light
**источник оттенков серого** — это небольшая версия стиля схемы дороги.

![стиль схемы оттенков серого](./media/supported-map-styles/grayscale_light.png)

**Применимые API:**
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android


## <a name="night"></a>ночь
**Ночь** — это темная версия стиля дорожной карты с цветными дорогами и символами.

![стиль карт ночью](./media/supported-map-styles/night.png)

**Применимые API:**
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android

## <a name="road_shaded_relief"></a>road_shaded_relief
**road shaded relief** — это основной стиль Azure Maps, дополненный рельефами Земли.

![Затененный стиль карт рельефов](./media/supported-map-styles/shaded-relief.png)

**Применимые API:**
* [Фрагмент карты](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android


## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как задать стиль схемы в Azure Maps.

> [!div class="nextstepaction"]
> [Выбор стиля карты](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
