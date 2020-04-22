---
title: Использование S2S VPN в качестве резервного копирования для частных пирингов Azure ExpressRoute (ru) Документы Майкрософт
description: На этой странице приведены архитектурные рекомендации для резервного копирования Azure ExpressRoute частных пиринг с S2S VPN.
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687821"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Использование S2S VPN в качестве резервного копирования для частного пиринга ExpressRoute

В статье под названием [Проектирование для аварийного восстановления с ExpressRoute частных пиринг][DR-PP], мы обсудили необходимость резервного подключения решение для ExpressRoute частных пиринг подключения и как использовать гео-излишние схемы ExpressRoute для этой цели. В этой статье, давайте рассмотрим, как использовать и поддерживать сайт на сайт (S2S) VPN в качестве спины для ExpressRoute частных пиринг. 

В отличие от геоизлишних схем ExpressRoute, вы можете использовать комбинацию аварийного восстановления ExpressRoute-VPN только в активно-пассивном режиме. Основная проблема использования любого резервного подключения к сети в пассивном режиме заключается в том, что пассивное соединение часто не работает вместе с основным соединением. Распространенной причиной сбоев пассивного соединения является отсутствие активного обслуживания. Поэтому в этой статье давайте сосредоточимся на том, как проверить и активно поддерживать S2S VPN подключение, которое резервное копирование ExpressRoute частных пиринга.

>[!NOTE] 
>Когда данный маршрут рекламируется как через ExpressRoute, так и VPN, Azure предпочел бы маршрут из ExpressRoute.  
>

В этой статье рассмотрим, как проверить подключение как с точки зрения Azure, так и с точки зрения края сети клиента. Возможность проверки с обоих сторон поможет независимо от того, управляете ли вы устройствами сети на стороне клиента, которые используются в сетевых объектах Майкрософт. 

## <a name="example-topology"></a>Пример топологии

В нашей настройке у нас есть локальная сеть, подключенная к концентратору Azure VNet как через схему ExpressRoute, так и через VPN соединение S2S. Концентратор Azure VNet, в свою очередь, заглянул в spoke VNet, как показано на диаграмме ниже:

![1][1]

В настройке схема ExpressRoute завершается на паре маршрутизаторов "Customer Edge" (CE) в помещении. Предприимчивая LAN подключена к маршрутизаторам CE через пару брандмауэров, работающих в режиме лидера-последователя. S2S VPN напрямую прекращается на брандмауэрах.

В следующей таблице перечислены ключевые префиксы иС топологии:

| **Сущности** | **Префикс** |
| --- | --- |
| На территории LAN | 10.1.11.0/25 |
| Azure Hub VNet | 10.17.11.0/25 |
| Azure говорил VNet | 10.17.11.128/26 |
| На территории испытательного сервера | 10.1.11.10 |
| Тест VNet | 10.17.11.132 |
| ExpressRoute первичное соединение p2p подсеть | 192.168.11.16/30 |
| ExpressRoute вторичное соединение p2p подсеть | 192.168.11.20/30 |
| VPN шлюз первичного BGP сверстников IP | 10.17.11.76 |
| VPN шлюз вторичного BGP сверстников IP | 10.17.11.77 |
| На территории брандмауэра VPN BGP одноранговой IP | 192.168.11.88 |
| Первичный маршрутизатор CE i/f к IP-накры | 192.168.11.0/31 |
| Брандмауэр i/f к основному IP-адресау CE | 192.168.11.1/31 |
| Вторичный маршрутизатор CE i/f к IP-налету брандмауэра | 192.168.11.2/31 |
| Брандмауэр i/f к вторичному IP-маршрутизатору CE | 192.168.11.3/31 |


В следующей таблице перечислены ASNs топологии:

| **Автономная система** | **Asn** |
| --- | --- |
| В локальной среде | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Виртуальная сеть GW (ExR) | 65515 |
| Виртуальная сеть GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Высокая доступность без асимметрии

### <a name="configuring-for-high-availability"></a>Настройка для высокой доступности

[Настройка ExpressRoute и сосуществующих соединений от сайта][Conf-CoExist] к месту обсуждает, как настроить сосуществующую схему ExpressRoute и VPN-соединения S2S. Как мы обсуждали в [проектировании высокой доступности с ExpressRoute][HA], для улучшения ExpressRoute высокой доступности нашей установки поддерживает избыточность сети (избегает одной точки сбоя) вплоть до конечных точек. Кроме того, как первичные, так и вторичные соединения схем ExpressRoute настроены для работы в активно-активном режиме, рекламируя префиксы на месте таким же образом через оба соединения. 

Реклама маршрута в помещениях основного маршрутизатора CE через первичное соединение схемы ExpressRoute показана ниже (команды Junos):

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Реклама маршрута вторичного маршрутизатора CE через вторичное соединение схемы ExpressRoute показана ниже (команды Junos):

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Чтобы повысить высокую доступность резервного соединения, S2S VPN также настроен в активно-активном режиме. Ниже показана конфигурация VPN-шлюза Azure. Обратите внимание, как часть конфигурации VPN BGP сверстников IP-адреса шлюза--10.17.11.76 и 10.17.11.77 - также перечислены.

![2][2]

Маршрут в предварительном помещении рекламируется брандмауэрами для первичных и вторичных узлов BGP VPN шлюза. Объявления о маршруте показаны ниже (Junos):

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>Настройка S2S VPN в активном режиме не только обеспечивает высокую доступность для подключения к резервному копированию аварийного восстановления, но и обеспечивает более высокую пропускную готовность к подключению резервного копирования. Другими словами, рекомендуется настройка S2S VPN в активно-активном режиме, поскольку она заставляет создавать несколько основных туннелей.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Настройка для симметричного потока трафика

Мы отметили, что, когда данный маршрут будет рекламироваться как через ExpressRoute, так и через S2S VPN, Azure предпочел бы путь ExpressRoute. Чтобы заставить Azure отдавать предпочтение S2S VPN-пути по сравнению с сосуществующей ExpressRoute, необходимо рекламировать более конкретные маршруты (более длинная приставка с большой подсетевой маской) через VPN-соединение. Наша цель здесь заключается в использовании VPN-соединений, как только обратно. Таким образом, поведение azure выбора путей по умолчанию соответствует нашей цели. 

Мы несем ответственность за то, чтобы трафик, предназначенный для Azure из помещений, также предпочитал путь ExpressRoute по сравнению с S2S VPN. Локальное предпочтение маршрутизаторов CE и брандмауэров в локальной настройке по умолчанию составляет 100. Таким образом, путем настройки локальных предпочтений маршрутов, полученных через частные пиринги ExpressRoute, превышающие 100 (скажем, 150), мы можем сделать трафик, предназначенный для Azure, предпочитают схему ExpressRoute в стабильном состоянии.

Ниже приведена конфигурация BGP основного маршрутизатора CE, которая завершает основное соединение схемы ExpressRoute. Обратите внимание, что значение локальных предпочтений маршрутов, рекламируемых в ходе сессии iBGP, настроено на 150. Аналогичным образом, мы должны обеспечить локальное предпочтение вторичного маршрутизатора CE, который завершает вторичное соединение схемы ExpressRoute также настроен о 150.

    user@SEA-MX03-01> show configuration routing-instances Cust11 
    description "Customer 11 VRF";
    instance-type virtual-router;
    interface xe-0/0/0:0.110;
    interface ae0.11;
    protocols {
      bgp {
        group ibgp {
            type internal;
            local-preference 150;
            neighbor 192.168.11.1;
        }
        group ebgp {
            peer-as 12076;
            bfd-liveness-detection {
                minimum-interval 300;
                multiplier 3;
            }
            neighbor 192.168.11.18;
        }
      }
    }

Таблица маршрутификаторов локтевых брандмауэров подтверждает (см. ниже), что для локтевого трафика, предназначенного для Azure, предпочтительный путь находится над ExpressRoute в устойчивом состоянии.

    user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
    + = Active Route, - = Last Active, * = Both

    10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119
    10.17.11.76/32     *[Static/5] 2d 21:12:16
                        > via st0.118
    10.17.11.77/32     *[Static/5] 2d 00:41:56
                        > via st0.119
    10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119

В приведенной выше таблице маршрутов, для концентратора и говорил VNet маршрутов--10.17.11.0/25 и 10.17.11.128/26 - мы видим, ExpressRoute схема предпочтительнее, чем VPN соединений. 192.168.11.0 и 192.168.11.2 являются МП на интерфейсе брандмауэра к маршрутизаторам CE.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Проверка обмена маршрутами через S2S VPN

Ранее в этой статье мы проверили рекламу на территории брандмауэра для первичных и вторичных узлов BGP VPN шлюза. Кроме того, давайте подтвердим маршруты Azure, полученные брандмауэрами от первичных и вторичных узлов BGP VPN шлюза.

    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.76                             65515 I
      10.17.11.128/26         10.17.11.76                             65515 I

    {primary:node0}
    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.77                             65515 I
      10.17.11.128/26         10.17.11.77                             65515 I

Аналогичным образом давайте проверим префиксы маршрутов на месте, полученные шлюзом Azure VPN. 

    PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

    Network      NextHop       AsPath      Weight
    -------      -------       ------      ------
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.76   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.77   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769

Как видно из выше, VPN шлюз имеет маршруты, полученные как первичных и вторичных КОЛЛЕГ BGP из VPN шлюза. Он также имеет видимость над маршрутами, полученными через первичные и вторичные соединения ExpressRoute (те, с AS-путь, подготовенных с 12076). Чтобы подтвердить маршруты, полученные через VPN-соединения, мы должны знать, на месте BGP сверстников IP соединений. В нашей установке рассматривается, это 192.168.11.88, и мы видим маршруты, полученные от него.

Далее давайте проверим маршруты, рекламируемые шлюзом Azure VPN к предварительному брандмуру BGP (192.168.11.88).

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


Неявка в обмен маршрутами указывает на сбой соединения. [См. Устранение: VPN-соединение Azure не может подключиться и перестанет работать][VPN Troubleshoot] на помощь в устранении неполадок в подключении VPN.

## <a name="testing-failover"></a>Сбой тестирования

Теперь, когда мы подтвердили успешные обмены маршрутами по VPN-соединению (план управления), мы настроены на переключение трафика (данные плоскости) от подключения ExpressRoute к VPN-соединению. 

>[!NOTE] 
>В производственных средах тестирование на сбой должно быть сделано во время запланированного обслуживания сети рабочего окна, как это может быть службы разрушительным.
>

Прежде чем сделать переключатель трафика, давайте прослежим текущий путь в нашей настройке от внутреннего тестового сервера до тестового VM в spoke VNet.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

Первичные и вторичные подсети экспресс-сообщения ExpressRoute от точки к точке нашей установки, соответственно, 192.168.11.16/30 и 192.168.11.20/30. В приведенном выше маршруте трассы, в шаге 3 мы видим, что мы бьем 192.168.11.18, который является интерфейсом IP первичного MSEE. Наличие интерфейса MSEE подтверждает, что, как и ожидалось, наш текущий путь находится над ExpressRoute.

Как сообщается в [спуске ExpressRoute цепи peerings][RST], давайте использовать следующие команды Powershell отключить как первичное, так и вторичное пиринг схемы ExpressRoute.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Время переключения сбоя зависит от времени конвергенции BGP. В нашей установке переключение переключения сбоя занимает несколько секунд (менее 10). После переключения повторение трассы показывает следующую траекторию:

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

Результат трассировки подтверждает, что резервное соединение через S2S VPN является активным и может обеспечить непрерывность обслуживания, если как первичные, так и вторичные подключения ExpressRoute не удается. Для завершения тестирования, препятствуйте включаем соединения ExpressRoute назад и нормализуем поток движения, используя следующий комплект команд.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Чтобы подтвердить движение переключается обратно на ExpressRoute, повторите трассу и убедитесь, что он проходит через ExpressRoute частных пиринг.

## <a name="next-steps"></a>Следующие шаги

ExpressRoute предназначен для высокой доступности без единой точки сбоя в сети Microsoft. Тем не менее схема ExpressRoute ограничена одним географическим регионом и поставщиком услуг. S2S VPN может быть хорошим решением для аварийного восстановления пассивного резервного копирования для схемы ExpressRoute. Для надежного решения для пассивного подключения к резервному копированию важно регулярное обслуживание пассивной конфигурации и периодическая проверка соединения. Важно не допустить, чтобы конфигурация VPN стала устаревшей, и периодически (скажем, каждый квартал) повторять проверку и неудачные тестовые шаги, описанные в этой статье во время окна обслуживания.

Для обеспечения мониторинга и оповещения на основе метрик ШЛюза VPN смотрите [настройки оповещений на метриках VPN Gateway.][VPN-alerts]

Чтобы ускорить конвергенцию BGP после сбоя ExpressRoute, [настройте BFD на ExpressRoute.][BFD]

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "топология в стадии рассмотрения"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Конфигурация VPN GW"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



