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
ms.openlocfilehash: ad75b333fc7d5d37a4ac074727e4d54b67bd0321
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45637250"
---
#### <a name="expressroute-limits"></a>Ограничения ExpressRoute
Ниже приведены ограничения, которые применяются к ресурсам ExpressRoute для каждой подписки.

| Ресурс | По умолчанию и максимальное число |
| --- | --- |
| Каналы ExpressRoute для каждой подписки |10 |
| Каналы ExpressRoute для каждого региона подписки Azure Resource Manager |10 |
| Максимальное количество маршрутов для частного пиринга Azure со стандартом ExpressRoute |4000 |
| Максимальное количество маршрутов для частного пиринга Azure с надстройкой ExpressRoute Premium |10 000 |
| Максимальное количество маршрутов для общедоступного пиринга Майкрософт в Azure со стандартом ExpressRoute |200 |
| Максимальное количество маршрутов для общедоступного пиринга Майкрософт в Azure с надстройкой ExpressRoute Premium |200 |
| Максимальное число каналов ExpressRoute, связанных с одной и той же виртуальной сетью в разных расположениях пиринга |4. |
| Количество связей виртуальных сетей на канал ExpressRoute |см. таблицу ниже |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Количество виртуальных сетей на канал ExpressRoute
| **Размер канала** | **Число связей виртуальных сетей для стандартной установки** | **Число связей виртуальных сетей с надстройкой Premium** |
| --- | --- | --- |
| 50 Мбит/с |10 |20 |
| 100 Мбит/с |10 |25 |
| 200 Мбит/с |10 |25 |
| 500 Мбит/с |10 |40 |
| 1 Гбит/с |10 |50 |
| 2 Гбит/с |10 |60 |
| 5 Гбит/с |10 |75 |
| 10 Гбит/с |10 |100 |

