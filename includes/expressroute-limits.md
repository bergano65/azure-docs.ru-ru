---
title: включить файл
description: включить файл
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334760"
---
| Ресурс | Ограничение |
| --- | --- |
| Каналы ExpressRoute для каждой подписки |10 |
| Схемы ExpressRoute в регионе за подписку с менеджером ресурсов Azure |10 |
| Максимальное количество маршрутов, рекламируемых в частный azure, с помощью ExpressRoute Standard |4000 |
| Максимальное количество маршрутов, рекламируемых в частный притон Azure с надстройкой ExpressRoute Premium |10 000 |
| Максимальное количество маршрутов, рекламируемых из частного приватного azure из адресного пространства VNet для подключения ExpressRoute |200 |
| Максимальное количество маршрутов, рекламируемых в Microsoft, с помощью ExpressRoute Standard |200 |
| Максимальное количество маршрутов, рекламируемых в Microsoft, пиринг с ExpressRoute Premium дополнения |200 |
| Максимальное количество схем ExpressRoute, связанных с одной и той же виртуальной сетью в одном и том же месте пиринга |4 |
| Максимальное число каналов ExpressRoute, связанных с одной и той же виртуальной сетью в разных расположениях пиринга |4 |
| Количество связей виртуальных сетей на канал ExpressRoute |Посмотреть [количество виртуальных сетей на таблицу схем ExpressRoute.](#vnetpercircuit)  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Количество виртуальных сетей на схему ExpressRoute
| **Размер цепи** | **Количество виртуальных сетевых ссылок для Standard** | **Количество виртуальных сетевых ссылок с надстройкой Premium** |
| --- | --- | --- |
| 50 Мбит/с |10 |20 |
| 100 Мбит/с |10 |25 |
| 200 Мбит/с |10 |25 |
| 500 Мбит/с |10 |40 |
| 1 Гбит/с |10 |50 |
| 2 Гбит/с |10 |60 |
| 5 Гбит/с |10 |75 |
| 10 Гбит/с |10 |100 |
| 40 Гбит/с |10 |100 |
| 100 Гбит/с |10 |100 |

**100 Гбит/с ExpressRoute Прямой Только*

> [!NOTE]
> Соединения Global Reach учитываются в пределах виртуальных сетевых подключений на трассе ExpressRoute. Например, Premium Circuit с 10 Гбит/с позволит обеспечить 5 соединений Global Reach и 95 соединений с шлюзами ExpressRoute или 95 соединениями Global Reach и 5 соединениями с шлюзами ExpressRoute или любой другой комбинацией до предела 100 соединений для цепи.
