---
title: 'Azure ExpressRoute: Мониторинг, метрика и оповещения'
description: Эта страница содержит сведения о мониторинге в ExpressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436914"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Мониторинг, метрики и оповещения в ExpressRoute

Эта статья даст вам представление о том, как с помощью Azure Monitor работают мониторинг, метрики и оповещения в ExpressRoute. Azure Monitor— это централизованная служба метрик, оповещений, журналов диагностики для всех продуктов Azure.
 
>[!NOTE]
>Не рекомендуется использовать **классические метрики**.
>

## <a name="expressroute-metrics"></a>Метрики ExpressRoute

Чтобы просмотреть **метрики,** перейдите на страницу *Azure Monitor* и нажмите *Metrics.* Для просмотра метрик **ExpressRoute,** фильтр по схемам типа ресурса *ExpressRoute.* Для просмотра метрик **Глобального Охвата** отфильтруйте *по схемам* Типа Ресурсов ExpressRoute и выберите ресурс схемы ExpressRoute с включенным Global Reach. Для просмотра **метрик ExpressRoute Direct,** фильтруйте тип ресурсов по *портам ExpressRoute.* 

После выбора метрики будет применена агрегация по умолчанию. Дополнительно можно применить расщепление, которое покажет метрику с различными размерами.

### <a name="available-metrics"></a>Доступные показатели
|**Метрические**|**Категория**|**Измерение (ы)**|**Характеристика (ы)**|
| --- | --- | --- | --- |
|Доступность ARP|Доступность|<ui><li>Peer (Первичный/вторичный маршрутизатор ExpressRoute)</ui></li><ui><li> Тип пиринга (частный/публичный/Microsoft)</ui></li>|ExpressRoute|
|Доступность Bgp|Доступность|<ui><li> Peer (Первичный/вторичный маршрутизатор ExpressRoute)</ui></li><ui><li> Тип пиринга</ui></li>|ExpressRoute|
|BitsInPerSecond|Трафик|<ui><li> Тип пиринга (ExpressRoute)</ui></li><ui><li>Ссылка (ExpressRoute Прямая)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Трафик| <ui><li>Тип пиринга (ExpressRoute)</ui></li><ui><li> Ссылка (ExpressRoute Прямая) | <ui><li>Expressroute<ui><li>ЭкспрессРут Прямая</ui></li> |
|GlobalReachBitsInВторая|Трафик|<ui><li>Peered Circuit Skey (Сервисный ключ)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Трафик|<ui><li>Peered Circuit Skey (Сервисный ключ)</ui></li>|Global Reach|
|Админгосударство|Физическая связь|Ссылка|ExpressRoute Direct|
|Линейныйпротокол|Физическая связь|Ссылка|ExpressRoute Direct|
|RxLightLevel|Физическая связь|<ui><li>Ссылку</ui></li><ui><li>Полосы</ui></li>|ExpressRoute Direct|
|TxLightLevel|Физическая связь|<ui><li>Ссылку</ui></li><ui><li>Полосы</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Использование *GlobalGlobalReachBitsInPerSecond* и *GlobalGlobalReachBitsOutPerSecond* будет видно только в том случае, если будет установлено хотя бы одно соединение Global Reach.
>

## <a name="circuits-metrics"></a>Метрики цепей

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits In and Out - Метрика во всех пирингах

Вы можете просматривать метрики во всех пирингах на данной схеме ExpressRoute.

![метрики канала](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits In and Out - Метрика за пиринг

Доступны метрики по частному и общедоступному пирингу, а также пирингу Майкрософт в бит/с.

![метрики пиринга](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Доступность BGP - Разделение по Peer  

Вы можете просматривать рядом с доступностью BGP в режиме реального времени через пиринги и одноранговые (первичные и вторичные маршрутизаторы ExpressRoute). Эта панель мониторинга показывает сессию Primary BGP для приватного пиринга и вторую сессию BGP вниз для приватного пиринга. 

![Доступность BGP на одноранговой](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Доступность ARP - Разделение по пирингу  

Вы можете просматривать рядом с доступностью [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) в режиме реального времени через пиринги и одноранговые (первичные и вторичные маршрутизаторы ExpressRoute). Эта панель мониторинга показывает приватную сессию ARP для обоих узлов, но завершается для Microsoft, вглядываясь в пиринг. Агрегация по умолчанию (средний) использовалась в обоих одноранговых узлах.  

![Доступность ARP на одноранговой](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Прямая метрика ExpressRoute

### <a name="admin-state---split-by-link"></a>Состояние админ - Разделение по ссылке
Вы можете просмотреть состояние админа для каждой ссылки пары прямого порта ExpressRoute.

![ER прямое состояние админ](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Биты в секунду - Сплит по ссылке
Вы можете просматривать биты в секунду по обе ссылки пары expressRoute Direct порта. 

![er прямые биты в секунду](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bits Out в секунду - Сплит по ссылке
Вы также можете просматривать биты в секунду по обе ссылки пары ExpressRoute Direct порта. 

![er прямые биты в секунду](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Протокол строки - Разделение по ссылке
Вы можете просмотреть линейный протокол по каждой ссылке пары портов ExpressRoute Direct.

![er протокол прямой линии](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Уровень света Rx - Разделение по ссылке
Вы можете просмотреть уровень света Rx (уровень света, который **получает**порт ExpressRoute Direct) для каждого порта. Здоровые уровни света Rx обычно подпадают в диапазоне от -10 до 0 дБ

![er прямой линии Rx Light Level](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Уровень tx Light - Разделение по ссылке
Вы можете просмотреть уровень света Tx (уровень света, который **транслирует**прямой порт ExpressRoute) для каждого порта. Здоровые уровни tx света обычно подпадают в диапазоне от -10 до 0 дБ

![er прямой линии Rx Light Level](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

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
  
  * [Создание и изменение канала](expressroute-howto-circuit-arm.md)
  * [Создание и изменение конфигурации пиринга](expressroute-howto-routing-arm.md)
  * [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)
