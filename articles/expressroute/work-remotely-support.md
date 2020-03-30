---
title: Использование Azure ExpressRoute для поддержки удаленных пользователей
description: На этой странице описано, как можно использовать Azure ExpressRoute для удаленной работы из-за пандемии COVID-19.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336651"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Использование Azure ExpressRoute для создания гибридного подключения для поддержки удаленных пользователей

В этой статье описывается, как можно использовать ExpressRoute, Azure, сеть Майкрософт и партнерскую экосистему Azure для удаленной работы.

## <a name="connecting-to-azure-services-with-expressroute"></a>Подключение к службам Azure с помощью ExpressRoute

>[!NOTE]
>В этой статье описывается, как можно использовать ExpressRoute, Azure, сеть Майкрософт и партнерскую экосистему Azure для удаленной работы и смягчения проблем сети, с которыми вы сталкиваетесь из-за кризиса COVID-19.
>

[ExpressRoute](expressroute-introduction.md) — это служба Azure, которая обеспечивает частную связь между центрами обработки данных майкрософт и инфраструктурой, которая находится в вашем помещении или в объекте colocation. Подключения ExpressRoute не проходят через общедоступный Интернет. Они обеспечивают безопасную связь, надежность и скорость, с более низкими и последовательными просрочками, чем обычные подключения через Интернет.

## <a name="useful-links"></a>Полезные ссылки

* [Как увеличить пропускную способность для существующей схемы ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [Мониторинг, метрики и оповещения ExpressRoute](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Оптимизация маршрута через ExpressRoute](expressroute-optimize-routing.md)
* [Azure ExpressRoute для O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Соображения асимметричной реукторов](expressroute-asymmetric-routing.md)
* [Как открыть запрос на поддержку на портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Устранение неполадок

* [Проверка подключения ExpressRoute](expressroute-troubleshooting-expressroute-overview.md).
* Получение таблицы ARP в [ресурсо-менеджере](expressroute-troubleshooting-arp-resource-manager.md) и [классике](expressroute-troubleshooting-arp-classic.md)
* [Сбросить неудавшуюся цепь](reset-circuit.md)
* [Производительность сети устранения неполадок](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Next Steps

* [Узнайте о моделях подключения ExpressRoute](expressroute-connectivity-models.md)
* Узнайте больше о подключениях ExpressRoute и доменах маршрутизации. Посмотреть [схемы ExpressRoute и домены маршрутизаторов](expressroute-circuit-peerings.md)
* Найти поставщика услуг. Посмотреть [партнеров ExpressRoute и местопроведения](expressroute-locations.md)
* Убедитесь, что выполнены все необходимые условия. Ознакомьтесь с разделом [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).
* [Создание и изменение канала ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
* [Создание и изменение пиринга для канала ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Подключение виртуальной сети к каналу ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
