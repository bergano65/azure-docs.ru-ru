---
title: Предварительные требования ExpressRoute Azure | Документация Майкрософт
description: На этой странице приведен список требований, которые необходимо выполнить, прежде чем заказать канал ExpressRoute. Здесь также приведен контрольный список.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4ef1a768356078c160b798c1a96412adda0330f6
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123294"
---
# <a name="expressroute-prerequisites--checklist"></a>Предварительные требования и контрольный список для ExpressRoute
Чтобы подключиться к службам Microsoft Cloud через ExpressRoute, вам нужно выполнить условия в следующих разделах.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Учетная запись Azure
* Наличие действительной и активной учетной записи Microsoft Azure. Она необходима для организации канала ExpressRoute. Каналы ExpressRoute являются ресурсами в подписках Azure. Подписка Azure является обязательной, даже если подключение ограничено облачными службами Майкрософт, отличными от Azure, например Office 365.
* Активная подписка на Office 365 (при использовании служб Office 365). Дополнительные сведения об определенных требованиях к Office 365 см. в соответствующем разделе этой статьи.

## <a name="connectivity-provider"></a>Поставщик услуг подключения

* Чтобы подключиться к Microsoft Cloud, можно работать с [партнером по подключению ExpressRoute](expressroute-locations.md#partners) . Подключение между локальной сетью и Майкрософт можно настроить [тремя различными способами](expressroute-introduction.md).
* Если ваш поставщик не является партнером по подключению ExpressRoute, вы можете подключиться к Microsoft Cloud через [поставщика облачных служб Exchange](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Требования к сети
* **Избыточность в каждом одноранговом расположении**: Корпорации Майкрософт требуются избыточные сеансы BGP для настройки маршрутизаторов корпорации Майкрософт и маршрутизаторов пиринга в каждом канале ExpressRoute (даже при наличии всего [одного физического подключения к облачному Exchange](expressroute-faqs.md#onep2plink)).
* **Избыточность для аварийного восстановления**: Корпорация Майкрософт настоятельно рекомендует настроить по крайней мере две цепи ExpressRoute в разных расположениях пиринга, чтобы избежать единой точки отказа.
* **Маршрутизация**. В зависимости от способа подключения к Microsoft Cloud вам или вашему поставщику необходимо настроить и администрировать сеансы BGP для [доменов маршрутизации](expressroute-circuit-peerings.md). Некоторые поставщики подключений Ethernet или облачных услуг Exchange могут предлагать управление BGP в качестве услуги с добавленной стоимостью.
* **Преобразование сетевых адресов (NAT).** Корпорация Майкрософт принимает только общедоступные IP-адреса через пиринг. Если в локальной сети используются частные IP-адреса, вам или вашему поставщику услуг необходимо преобразовать их в общедоступные IP-адреса [с помощью NAT](expressroute-nat.md).
* **Качество обслуживания.** Skype для бизнеса предоставляет различные услуги (например, передача голоса, видео, текста), которые требуют дифференцированного подхода к качеству обслуживания. Вам и вашему поставщику услуг необходимо соблюдать [требования к качеству обслуживания](expressroute-qos.md).
* **Сетевая безопасность.** При подключении к Microsoft Cloud через ExpressRoute необходимо помнить о [сетевой безопасности](../best-practices-network-security.md).

## <a name="office-365"></a>Office 365
Если вы планируете включить Office 365 через ExpressRoute, просмотрите следующие документы для получения дополнительных сведений о требованиях к Office 365.

* [Azure ExpressRoute для Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Маршрутизация с помощью ExpressRoute для Office 365](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Высокий уровень доступности и отработка отказов при использовании Azure ExpressRoute](https://aka.ms/erhighavailability)
* [URL-адреса и диапазоны IP-адресов Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Сетевое планирование и настройка производительности для Office 365](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Планирование сети и миграции при переходе на Office 365](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Интеграция Office 365 с локальными средами](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Расширенные учебные видеоматериалы об использовании ExpressRoute в Office 365](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Следующие шаги
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Найдите поставщика услуг подключения ExpressRoute. См. статью [Партнеры и одноранговые расположения ExpressRoute](expressroute-locations.md).
* Ознакомьтесь с требованиями к [маршрутизации](expressroute-routing.md), [NAT](expressroute-nat.md) и [качеству обслуживания](expressroute-qos.md).
* Настройте подключение ExpressRoute.
  * [Создайте канал ExpressRoute.](expressroute-howto-circuit-arm.md)
  * [Настройка маршрутизации](expressroute-howto-routing-arm.md)
  * [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)
