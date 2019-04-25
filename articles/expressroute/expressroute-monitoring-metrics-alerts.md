---
title: Мониторинг, метрики и оповещения в Azure ExpressRoute | Документация Майкрософт
description: Эта страница содержит сведения о мониторинге в ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: d78c110f3317f4dd9f16cbe243aeca437e9890a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60364707"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Мониторинг, метрики и оповещения в ExpressRoute

Эта статья даст вам представление о том, как с помощью Azure Monitor работают мониторинг, метрики и оповещения в ExpressRoute. Azure Monitor— это централизованная служба метрик, оповещений, журналов диагностики для всех продуктов Azure.
 
>[!NOTE]
>Не рекомендуется использовать **классические метрики**.
>

## <a name="circuit-metrics"></a>Метрики канала

Чтобы перейти в подраздел **Метрики**, откройте страницу канала ExpressRoute, который хотите отслеживать. В разделе **Мониторинг** вы найдете подраздел **Метрики**. Выберите BitsInPerSecond или BitsOutPerSecond и агрегирование. В случае необходимости можно применить разделение. При этом будут показаны метрики для каждого типа пиринга.

![метрики канала](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>Метрики пиринга

Доступны метрики по частному и общедоступному пирингу, а также пирингу Майкрософт в бит/с.

![метрики пиринга](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Подключения шлюза ExpressRoute в бит/с

![подключения шлюза](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>Оповещения для подключений шлюза ExpressRoute

1. Чтобы настроить оповещения, перейдите к **Azure Monitor** и щелкните **Оповещения**.

   ![оповещения](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Щелкните **+Select Target** (Выбрать целевой объект) и выберите ресурс подключения шлюза ExpressRoute.

   ![target]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Указание сведений для оповещения.

   ![группа действий](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Указание сведений для группы действий, добавление группы действий.

   ![добавить группу действий](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Оповещения для каждого пиринга

 ![что](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Настройка оповещений для журналов действий каналов

В раскрывающемся списке **Критерии оповещения** можно выбрать **Журнал действий** для определенного типа сигнала и указать сигнал.

  ![другое](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Дальнейшие действия

Настройте подключение ExpressRoute.
  
  * [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-arm.md)
  * [Создание и изменение пиринга для канала ExpressRoute с помощью PowerShell](expressroute-howto-routing-arm.md)
  * [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)
