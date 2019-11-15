---
title: Azure ExpressRoute. модели подключения
description: В этой статье описываются различные режимы подключения между сетью клиента и Microsoft Azure и службами Office 365. Клиенты могут использовать поставщиков MPLS, поставщиков облачных служб Exchange и поставщиков Ethernet.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: 375d2f9d3b455c0495c69f2b23d62b1ab6522710
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080696"
---
# <a name="expressroute-connectivity-models"></a>Модели подключения ExpressRoute
Вы можете создать между локальной сетью и облаком Майкрософт подключение трех разных типов: [совместное размещение в Cloud Exchange](#CloudExchange), [Ethernet-подключение типа "точка — точка"](#Ethernet) и [подключение типа "любой к любому" (IPVPN)](#IPVPN). Поставщики услуг подключения могут предлагать одну или несколько моделей подключения. Обратитесь к поставщику услуг подключения и подберите наиболее подходящую вам модель.
<br><br>

![Схема моделей подключения ExpressRoute](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="CloudExchange"></a>Совместное размещение в Cloud Exchange
При размещении на одном сервере с Cloud Exchange можно заказать виртуальное кросс-подключение к облаку Майкрософт через Ethernet Exchange поставщика совместного размещения. Поставщики услуг совместного размещения могут предлагать либо кросс-подключения второго уровня, либо управляемые кросс-подключения третьего уровня между вашей инфраструктурой на сервере совместного размещения и облаком Майкрософт.

## <a name="Ethernet"></a>Подключения Ethernet типа "точка — точка"
Локальные центры обработки данных и офисы можно подключить к облаку Майкрософт через связи Ethernet типа "точка-точка". Поставщики подключений Ethernet типа "точка-точка" могут предлагать подключения второго уровня или управляемые подключения третьего уровня между вашей сетью и облаком Майкрософт.

## <a name="IPVPN"></a>Сети типа "любой к любому" (IPVPN)
Глобальную вычислительную сеть можно интегрировать с облаком Майкрософт. Поставщики IP VPN (обычно это MPLS VPN) предлагают подключение между филиалами и центрами обработки данных типа "любой к любому". Облако Майкрософт можно связать с вашей глобальной вычислительной сетью, так чтобы оно выглядело как любой другой филиал. Обычно поставщики глобальных вычислительных сетей предлагают управляемые подключения третьего уровня. Возможности и функции ExpressRoute одинаковы для всех описанных выше моделей. 

## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше о подключениях ExpressRoute и доменах маршрутизации. См. статью [Каналы ExpressRoute и домены маршрутизации](expressroute-circuit-peerings.md).
* Узнайте о функциях ExpressRoute. См. статью [Технический обзор ExpressRoute](expressroute-introduction.md).
* Найти поставщика услуг. См. статью [Партнеры и одноранговые расположения ExpressRoute](expressroute-locations.md).
* Убедитесь, что выполнены все необходимые условия. Ознакомьтесь с разделом [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).
* См. сведения о требованиях для [маршрутизации](expressroute-routing.md) и [NAT](expressroute-nat.md) и [QoS](expressroute-qos.md).
* Настройте подключение ExpressRoute.
  * [Создание канала ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Настройка маршрутизации](expressroute-howto-routing-portal-resource-manager.md)
  * [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)