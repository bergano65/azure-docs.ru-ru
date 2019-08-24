---
title: Мониторинг, метрики и оповещения в Azure ExpressRoute | Документация Майкрософт
description: Эта страница содержит сведения о мониторинге в ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 92ec03e20fb6e681a0afd14048449ad004ebca0c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991472"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Мониторинг, метрики и оповещения в ExpressRoute

Эта статья даст вам представление о том, как с помощью Azure Monitor работают мониторинг, метрики и оповещения в ExpressRoute. Azure Monitor— это централизованная служба метрик, оповещений, журналов диагностики для всех продуктов Azure.
 
>[!NOTE]
>Не рекомендуется использовать **классические метрики**.
>

## <a name="circuit-metrics"></a>Метрики канала

Чтобы перейти в подраздел **Метрики**, откройте страницу канала ExpressRoute, который хотите отслеживать. В разделе **Мониторинг** вы найдете подраздел **Метрики**. Выберите из перечисленных ниже метрик. Будет применена Статистическая обработка по умолчанию. При необходимости можно применить разделение, в котором будут показаны метрики с разными измерениями.

### <a name="metrics-available"></a>Доступные метрики: 
* **Доступность** 
    * Доступность ARP
      * Доступные измерения:
        * Одноранговый (основной или дополнительный маршрутизатор ExpressRoute)
        * Тип пиринга (частный, общедоступный/Майкрософт)
    * Доступность BGP
      * Доступные измерения:
        * Одноранговый (основной или дополнительный маршрутизатор ExpressRoute)
        * Тип пиринга (частный, общедоступный/Майкрософт)
* **Трафика**
    * BitsInPerSecond
      * Доступные измерения:
        * Тип пиринга (частный, общедоступный/Майкрософт)
    * BitsOutPerSecond
      * Доступные измерения:
        * Тип пиринга (частный, общедоступный/Майкрософт)
    * глобалреачбитсинперсеконд
      * Доступные измерения:
        * Скэйная цепь (ключ службы)
    * глобалреачбитсаутперсеконд
      * Доступные измерения:
        * Скэйная цепь (ключ службы)

>[!NOTE]
>Использование *глобалглобалреачбитсинперсеконд* и *глобалглобалреачбитсаутперсеконд* будет видимым, только если установлено хотя бы одно Global REACH соединение.
>

## <a name="bits-in-and-out---metrics-across-all-peerings"></a>Входные и выходные метрики во всех пиринга

Вы можете просматривать метрики для всех узлов в заданном канале ExpressRoute.

![метрики канала](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="bits-in-and-out---metrics-per-peering"></a>Количество входных и исходящих битов в метрики на пиринг

Доступны метрики по частному и общедоступному пирингу, а также пирингу Майкрософт в бит/с.

![метрики пиринга](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="bgp-availability---split-by-peer"></a>Доступность BGP — разделение по узлу  

Вы можете просмотреть сведения о доступности BGP в режиме реального времени для пиринга и одноранговых узлов (основной и дополнительный маршрутизаторы ExpressRoute). На этой панели мониторинга показан основной сеанс BGP для частного пиринга и второй сеанс BGP для частного пиринга. 

![Доступность BGP на узел](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

## <a name="arp-availability---split-by-peering"></a>Доступность ARP — разделение по пиринга  

Вы можете просмотреть сведения о доступности [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) в режиме реального времени для пиринга и одноранговых узлов (основной и дополнительный маршрутизаторы ExpressRoute). На этой панели мониторинга показан сеанс ARP частного пиринга по обоим одноранговым узлам, но для пиринга Майкрософт между одноранговыми узлами завершается. По умолчанию для обоих узлов использовалась статистическая обработка (среднее значение).  

![Доступность ARP на одноранговый узел](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Подключения шлюза ExpressRoute в бит/с

![подключения шлюза](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>Оповещения для подключений шлюза ExpressRoute

1. Чтобы настроить оповещения, перейдите к **Azure Monitor** и щелкните **Оповещения**.

   ![оповещений](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Щелкните **+Select Target** (Выбрать целевой объект) и выберите ресурс подключения шлюза ExpressRoute.

   ![target;]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Указание сведений для оповещения.

   ![группа действий](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Указание сведений для группы действий, добавление группы действий.

   ![добавить группу действий](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Оповещения для каждого пиринга

 ![что](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Настройка оповещений для журналов действий каналов

В раскрывающемся списке **Критерии оповещения** можно выбрать **Журнал действий** для определенного типа сигнала и указать сигнал.

  ![другое](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Следующие шаги

Настройте подключение ExpressRoute.
  
  * [Создание и изменение канала](expressroute-howto-circuit-arm.md)
  * [Создание и изменение конфигурации пиринга](expressroute-howto-routing-arm.md)
  * [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)
