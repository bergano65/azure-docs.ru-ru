---
title: Поддерживаемые стили карт в службе Azure Maps | Документация Майкрософт
description: Стили карт, поддерживаемые в службе Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 10/02/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c8edaba8de597e3e76e760e1f5109006338a663c
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238826"
---
# <a name="azure-maps-supported-map-styles"></a>Стили карт, поддерживаемые в службе Azure Maps
Azure Maps поддерживает четыре различные встроенные стили карт. Ниже перечислены стили и их описания.

## <a name="road"></a>дорога
Карта **дороги** — это стандартная карта, которая отображает дороги, природные и искусственные объекты, а также ярлыки для этих объектов.

![дорога](./media/supported-map-styles/road.png)

**Применимые API:**
* [Изображение карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Фрагмент карты](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Элементы управления картой JS

## <a name="satellite"></a>спутник 
Стиль **спутник** представляет собой комбинацию спутниковых и аэроснимков.

![спутник](./media/supported-map-styles/satellite.png)

**Применимые API:**
* [Фрагмент спутниковой карты](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Элементы управления картой JS

## <a name="satelliteroadlabels"></a>satellite_road_labels
Этот стиль карты представляет собой гибрид дорог и ярлыков, наложенных поверх спутниковых и аэроснимков.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Применимые API:**
* Элементы управления картой JS

## <a name="grayscaledark"></a>grayscale_dark
**Темно-серый** — это темная версия стиля дорожной карты.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Применимые API:**
* Элементы управления картой JS 