---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211425"
---
Устаревшие версии (старые) SKU VPN-шлюзов:

* По умолчанию (основной)
* Standard
* HighPerformance.

VPN-шлюз не использует номер SKU UltraPerformance. Сведения об SKU UltraPerformance см. в документации по [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

При работе с устаревшими версиями SKU учитывайте указанные ниже факторы.

* Если вам нужен тип VPN на основе политик, используйте SKU "Базовый". Тип VPN на основе политик (ранее называемый статической маршрутизацией) не поддерживается в других номерах SKU.
* SKU "Базовый" не поддерживает BGP.
* Одновременное использование конфигураций VPN-шлюзов и ExpressRoute не поддерживается в SKU "Базовый".
* Активно-активные подключения типа "сайт — сайт" через VPN-шлюз можно настроить только в SKU "Высокая производительность".