---
title: Включить имя файла
description: включить файл
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334760"
---
| Ресурс | Ограничение |
| --- | --- |
| Каналы ExpressRoute для каждой подписки |10 |
| Цепи ExpressRoute на регион на подписку с Azure Resource Manager |10 |
| Максимальное число маршрутов, объявляемых для частного пиринга Azure с помощью ExpressRoute Standard |4000 |
| Максимальное число маршрутов, объявляемых для частного пиринга Azure с надстройкой ExpressRoute Premium |10 000 |
| Максимальное число маршрутов, объявленных из частного пиринга Azure из адресного пространства виртуальной сети для подключения ExpressRoute |200 |
| Максимальное число маршрутов, объявленных для пиринга Майкрософт с помощью ExpressRoute Standard |200 |
| Максимальное число маршрутов, объявляемых для пиринга Майкрософт с надстройкой ExpressRoute Premium |200 |
| Максимальное число каналов ExpressRoute, связанных с одной и той же виртуальной сетью в одном одноранговом расположении |4 |
| Максимальное число каналов ExpressRoute, связанных с одной и той же виртуальной сетью в разных расположениях пиринга |4 |
| Количество связей виртуальных сетей на канал ExpressRoute |См. [число виртуальных сетей на таблицу канала ExpressRoute](#vnetpercircuit) .  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Число виртуальных сетей на канал ExpressRoute
| **Размер цепи** | **Число связей виртуальных сетей для Standard** | **Число связей виртуальных сетей с надстройкой Premium** |
| --- | --- | --- |
| 50 Мбит/с |10 |20 |
| 100 Мбит/с |10 |25 |
| 200 Мбит/с |10 |25 |
| 500 Мбит/с |10 |40 |
| 1 Гбит/с |10 |50 |
| 2 Гбит/с |10 |60 |
| 5 Гбит/с |10 |75 |
| 10 Гбит/с |10 |100 |
| 40 Гбит/с * |10 |100 |
| 100 Гбит/с * |10 |100 |

**100 Гбит/с ExpressRoute только Direct*

> [!NOTE]
> Число подключений Global Reach ограничивается предельным числом подключений виртуальной сети на канал ExpressRoute. Например, канал с 10 Гбит/с (Premium) допускает 5 Global Reach подключений и 95 подключений к шлюзам ExpressRoute или 95 Global Reach подключений и 5 подключений к шлюзам ExpressRoute, а также любое другое сочетание с ограничением в 100 подключений для канала.
