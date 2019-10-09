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
ms.openlocfilehash: 457154a797a4b6d9853b1effe0d8121053653a99
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174763"
---
# <a name="azure-maps-supported-map-styles"></a>Стили карт, поддерживаемые в службе Azure Maps
Служба Azure Maps поддерживает несколько различных встроенных стилей карт, как описано ниже.

## <a name="road"></a>дорога
Карта **дороги** — это стандартная карта, которая отображает дороги, природные и искусственные объекты, а также ярлыки для этих объектов.

![дорога](./media/supported-map-styles/road.png)

**Применимые API:**
* [Изображение карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Фрагмент карты](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android

## <a name="blank-and-blank_accessible"></a>blank и blank_accessible

**Пустые** и **blank_accessibleные** стили карт предоставляют пустой холст для визуализации данных. В стиле **blank_accessible** будет по-прежнему предоставляться информация о расположении, где расположена схема, и в том случае, если базовая схема не отображается.

> [!Note]
> В веб-пакете SDK можно изменить цвет фона на карте, установив стиль CSS `background-color` элемента Map DIV.

**Применимые API:**
* Элемент управления картой веб-пакета SDK

## <a name="satellite"></a>спутник 
Стиль **спутник** представляет собой комбинацию спутниковых и аэроснимков.

![спутник](./media/supported-map-styles/satellite.png)

**Применимые API:**
* [Фрагмент спутниковой карты](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Этот стиль карты представляет собой гибрид дорог и ярлыков, наложенных поверх спутниковых и аэроснимков.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Применимые API:**
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android

## <a name="grayscale_dark"></a>grayscale_dark
**Темно-серый** — это темная версия стиля дорожной карты.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Применимые API:**
* Элемент управления картой веб-пакета SDK 
* Элемент управления картой Android


## <a name="grayscale_light"></a>grayscale_light
**источник оттенков серого** — это небольшая версия стиля схемы дороги.

![светло-серый](./media/supported-map-styles/grayscale_light.png)

**Применимые API:**
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android


## <a name="night"></a>ночь
**Ночь** — это темная версия стиля дорожной карты с цветными дорогами и символами.

![ночь](./media/supported-map-styles/night.png)

**Применимые API:**
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android

## <a name="road_shaded_relief"></a>road_shaded_relief
**road shaded relief** — это основной стиль Azure Maps, дополненный рельефами Земли.

![shaded relief](./media/supported-map-styles/shaded-relief.png)

**Применимые API:**
* [Фрагмент карты](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Элемент управления картой веб-пакета SDK
* Элемент управления картой Android


## <a name="next-steps"></a>Следующие шаги

Узнайте, как задать стиль схемы в Azure Maps.

> [!div class="nextstepaction"]
> [Выбор стиля карты](https://docs.microsoft.com/en-us/azure/azure-maps/choose-map-style)