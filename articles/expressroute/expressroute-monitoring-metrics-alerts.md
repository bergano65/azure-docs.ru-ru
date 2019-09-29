---
title: Мониторинг, метрики и оповещения в Azure ExpressRoute | Документация Майкрософт
description: Эта страница содержит сведения о мониторинге в ExpressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 40e5561c9a55595340568ec660cbc6dd6e1eab51
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672046"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Мониторинг, метрики и оповещения в ExpressRoute

Эта статья даст вам представление о том, как с помощью Azure Monitor работают мониторинг, метрики и оповещения в ExpressRoute. Azure Monitor— это централизованная служба метрик, оповещений, журналов диагностики для всех продуктов Azure.
 
>[!NOTE]
>Не рекомендуется использовать **классические метрики**.
>

## <a name="expressroute-metrics"></a>Метрики ExpressRoute

Чтобы просмотреть **метрики**, перейдите на страницу *Azure Monitor* и щелкните *метрики*. Чтобы просмотреть метрики **expressroute** , фильтр по типу ресурса *каналы ExpressRoute*. Чтобы просмотреть метрики **Global REACH** , отфильтруйте по типу ресурса *каналы expressroute* и выберите ресурс канала expressroute, в котором включено Global REACH. Чтобы просмотреть метрики **Direct expressroute** , отфильтруйте тип ресурса по *портам ExpressRoute*. 

После выбора метрики будет применено агрегирование по умолчанию. При необходимости можно применить разделение, которое будет показывать метрику с разными измерениями.

### <a name="available-metrics"></a>Доступные метрики
|**Метрика**|**Категория**|**Измерения (s)**|**Функции**|
| --- | --- | --- | --- |
|Доступность ARP|Доступность|<ui><li>Одноранговый (основной или дополнительный маршрутизатор ExpressRoute)</ui></li><ui><li> Тип пиринга (частный, общедоступный/Майкрософт)</ui></li>|ExpressRoute|
|Доступность BGP|Доступность|<ui><li> Одноранговый (основной или дополнительный маршрутизатор ExpressRoute)</ui></li><ui><li> Тип пиринга</ui></li>|ExpressRoute|
|BitsInPerSecond|Трафик|<ui><li> Тип пиринга (ExpressRoute)</ui></li><ui><li>Ссылка (с ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Трафик| <ui><li>Тип пиринга (ExpressRoute)</ui></li><ui><li> Ссылка (с ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>Непосредственный ExpressRoute</ui></li> |
|глобалреачбитсинперсеконд|Трафик|<ui><li>Скэйная цепь (ключ службы)</ui></li>|Global Reach|
|глобалреачбитсаутперсеконд|Трафик|<ui><li>Скэйная цепь (ключ службы)</ui></li>|Global Reach|
|админстате|Физическое подключение|Ссылка|ExpressRoute Direct|
|линепротокол|Физическое подключение|Ссылка|ExpressRoute Direct|
|ркслигхтлевел|Физическое подключение|<ui><li>Ссылку</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|ткслигхтлевел|Физическое подключение|<ui><li>Ссылку</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Использование *глобалглобалреачбитсинперсеконд* и *глобалглобалреачбитсаутперсеконд* будет видимым, только если установлено хотя бы одно Global REACH соединение.
>

## <a name="circuits-metrics"></a>Метрики каналов

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Входные и выходные метрики во всех пиринга

Вы можете просматривать метрики для всех узлов в заданном канале ExpressRoute.

![метрики канала](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Количество входных и исходящих битов в метрики на пиринг

Доступны метрики по частному и общедоступному пирингу, а также пирингу Майкрософт в бит/с.

![метрики пиринга](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Доступность BGP — разделение по узлу  

Вы можете просмотреть сведения о доступности BGP в режиме реального времени для пиринга и одноранговых узлов (основной и дополнительный маршрутизаторы ExpressRoute). На этой панели мониторинга показан основной сеанс BGP для частного пиринга и второй сеанс BGP для частного пиринга. 

![Доступность BGP на узел](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Доступность ARP — разделение по пиринга  

Вы можете просмотреть сведения о доступности [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) в режиме реального времени для пиринга и одноранговых узлов (основной и дополнительный маршрутизаторы ExpressRoute). На этой панели мониторинга показан сеанс ARP частного пиринга по обоим одноранговым узлам, но для пиринга Майкрософт между одноранговыми узлами завершается. По умолчанию для обоих узлов использовалась статистическая обработка (среднее значение).  

![Доступность ARP на одноранговый узел](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Непосредственные метрики ExpressRoute

### <a name="admin-state---split-by-link"></a>Состояние администратора — разбиение по каналу
Вы можете просмотреть состояние администратора для каждой ссылки на пару прямых портов ExpressRoute.

![состояние прямого администратора ER](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Число битов в секунду — разбиение по каналу
Можно просмотреть биты в секунду для обеих ссылок в паре прямых портов ExpressRoute. 

![число прямых бит в секунду для ER](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Биты за секунду — разбиение по каналу
Кроме того, можно просмотреть биты в секунду для обеих ссылок в паре прямых портов ExpressRoute. 

![непосредственных бит ER в секунду](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Протокол строки — разбиение по каналу
Протокол Line можно просмотреть по каждой ссылке на пару прямых портов ExpressRoute.

![Протокол прямой строки ER](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Уровень источника RX — разбиение по каналу
Для каждого порта можно просмотреть уровень падения на RX (уровень освещения, который **получает**прямой порт ExpressRoute). Работоспособные уровни освещения обычно находятся в диапазоне от-10 до 0 dBm.

![непосредственный уровень RX прямой строки ER](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Уровень источника TX — разбиение по каналу
Для каждого порта можно просмотреть уровень источника TX (уровень падения, который **передает**прямой порт ExpressRoute). Работоспособные уровни интенсивности TX обычно находятся в диапазоне от-10 до 0 dBm.

![непосредственный уровень RX прямой строки ER](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

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
