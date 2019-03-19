---
title: включение файла
description: включение файла
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fa448975a5275e5aab4dc82b75433d188422e507
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554195"
---
#### <a name="expressroute-limits"></a>Ограничения ExpressRoute
Следующие ограничения применяются к ресурсам Azure ExpressRoute для каждой подписки.

| Ресурс | По умолчанию и максимальные ограничения |
| --- | --- |
| Каналы ExpressRoute для каждой подписки |10 |
| Каналы ExpressRoute на регион на подписку, с помощью Azure Resource Manager |10 |
| Максимальное количество маршрутов для частного пиринга Azure со стандартом ExpressRoute |4000 |
| Максимальное количество маршрутов для частного пиринга Azure с надстройкой ExpressRoute Premium |10 000 |
| Максимальное количество маршрутов для пиринга Microsoft Azure со стандартом ExpressRoute |200 |
| Максимальное количество маршрутов для пиринга Microsoft Azure с надстройкой ExpressRoute Premium |200 |
| Максимальное число каналов ExpressRoute, связанных с одной и той же виртуальной сетью в разных расположениях пиринга |4. |
| Количество связей виртуальных сетей на канал ExpressRoute |См. в следующей таблице. |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Количество виртуальных сетей на канал ExpressRoute
| **Размер канала** | **Число связей виртуальных сетей для уровня Standard** | **Число связей виртуальных сетей с надстройкой Premium** |
| --- | --- | --- |
| 50 Мбит/с |10 |20 |
| 100 Мбит/с |10 |25 |
| 200 Мбит/с |10 |25 |
| 500 Мбит/с |10 |40 |
| 1 Гбит/с |10 |50 |
| 2 Гбит/с |10 |60 |
| 5 Гбит/с |10 |75 |
| 10 Гбит/с |10 |100 |

