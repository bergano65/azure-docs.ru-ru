---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: f2ee442d0d6fecf34449ad28f058615a1274bbea
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185175"
---
Устаревшие версии (старые) SKU VPN-шлюзов:

* базовая;
* Стандартная
* HighPerformance.

VPN-шлюз не использует номер SKU UltraPerformance. Сведения об SKU UltraPerformance см. в документации по [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

При работе с устаревшими версиями SKU учитывайте указанные ниже факторы.

* Если вам нужен тип VPN на основе политик, используйте SKU "Базовый". Тип VPN на основе политик (ранее называемый статической маршрутизацией) не поддерживается в других номерах SKU.
* SKU "Базовый" не поддерживает BGP.
* Одновременное использование конфигураций VPN-шлюзов и ExpressRoute не поддерживается в SKU "Базовый".
* Активно-активные подключения типа "сайт — сайт" через VPN-шлюз можно настроить только в SKU "Высокая производительность".