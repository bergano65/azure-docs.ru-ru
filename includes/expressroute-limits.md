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
ms.openlocfilehash: d067f1af3d5479aef28ddf2290cebe3fe45726b0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733792"
---
| Resource | По умолчанию и максимальные ограничения |
| --- | --- |
| Каналы ExpressRoute для каждой подписки |10 |
| Каналы ExpressRoute на регион на подписку, с помощью Azure Resource Manager |10 |
| Максимальное количество маршрутов для частного пиринга Azure со стандартом ExpressRoute |4000 |
| Максимальное количество маршрутов для частного пиринга Azure с надстройкой ExpressRoute Premium |10 000 |
| Максимальное количество маршрутов для частного пиринга Azure из адресного пространства виртуальной сети для подключения ExpressRoute |200 | 
| Максимальное количество маршрутов для пиринга Microsoft Azure со стандартом ExpressRoute |200 |
| Максимальное количество маршрутов для пиринга Microsoft Azure с надстройкой ExpressRoute Premium |200 |
| Максимальное число каналов ExpressRoute, связанных с одной и той же виртуальной сети в одном расположении пиринга |4. |
| Максимальное число каналов ExpressRoute, связанных с одной и той же виртуальной сетью в разных расположениях пиринга |> 4, зависит от размера подсеть шлюза|
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

