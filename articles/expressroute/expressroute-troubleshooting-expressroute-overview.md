---
title: 'Azure ExpressRoute: Проверить связь - Руководство по устранению неполадок'
description: В этой статье содержатся инструкции по устранению неполадок и проверке сквозного подключения канала ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 58ae39e8dfdf918ae14ca9bb8dac28405828999e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330963"
---
# <a name="verifying-expressroute-connectivity"></a>Проверка подключения ExpressRoute
Изучив сведения в этой статье, вы узнаете, как проверить и устранить неполадки с подключением ExpressRoute. ExpressRoute расширяет локальная сеть в облаке Майкрософт через частное соединение, которое обычно облегчается поставщиком подключения. Подключение ExpressRoute традиционно включает в себя три различные сетевые зоны, а именно:

-   клиентскую сеть;
-   сеть поставщика;
-   центр обработки данных Майкрософт.

> [!NOTE]
> В модели прямого подключения ExpressRoute (предлагается при пропускной способности 10/100 Гбит/с) клиенты могут напрямую подключиться к порту маршрутизаторов Microsoft Enterprise Edge (MSEE). Таким образом, в модели прямого подключения есть только зоны сети клиентов и Майкрософт.
>


Цель этого документа состоит в том, чтобы помочь пользователю определить, существует ли проблема подключения и где. Тем самым, чтобы помочь обратиться за поддержкой к соответствующей группе для решения проблемы. Если для устранения проблемы необходима помощь службы поддержки Майкрософт, отправьте запрос в [службу поддержки Майкрософт][Support].

> [!IMPORTANT]
> Данный документ предназначен для диагностики и устранения простых проблем. Он не заменит услуг службы поддержки Майкрософт. Если вам не удается устранить проблему, используя приведенные здесь рекомендации, то необходимо отправить запрос в [службу поддержки Майкрософт][Support].
>
>

## <a name="overview"></a>Обзор
На следующей схеме показано логическое подключение между клиентской сетью и сетью Майкрософт через ExpressRoute.
[![1]][1]

На предыдущей схеме цифры обозначают ключевые точки сети. Эти сетевые точки иногда упоминаются в этой статье по связанному с ними числу. В зависимости от модели подключения ExpressRoute - Совместное местоположение Обмена облаком, соединение От точки к точке Ethernet или любое-к-любому (IPVPN) - сетевые точки 3 и 4 могут быть коммутаторами (устройства слоя 2) или маршрутизаторами (устройства слоя 3). В модели прямого подключения нет сетевых точек 3 и 4; вместо CEs (2) непосредственно связаны с MSEEs через темное волокно. Ниже приведены представленные на схеме ключевые точки сети.

1.  Клиентское вычислительное устройство (например, сервер или компьютер).
2.  Клиентские пограничные маршрутизаторы (CE). 
3.  Пограничные маршрутизаторы (коммутаторы) поставщика услуг связи, подключенные к клиентским пограничным маршрутизаторам (PE, подключенные к CE). В этом документе называются PE-CE.
4.  Пограничные маршрутизаторы (коммутаторы) поставщика услуг связи, подключенные к MSEE. В этом документе называются PE-MSEE.
5.  Маршрутизаторы MSEE ExpressRoute.
6.  Шлюз виртуальной сети
7.  Вычислительное устройство в виртуальной сети Azure

Если используются совместное размещение облачных бирж, модели Point-to-Point Ethernet или модели прямого подключения, CE (2) устанавливают BGP, вглядываясь в MSEEs (5). 

При использовании модели подключения any-to-any (IPVPN) PE-MSEEs (4) устанавливает BGP с ПОМОЩЬю МСП (5). PE-MSEEs распространяют маршруты, полученные от корпорации Майкрософт обратно в клиентскую сеть через сеть поставщиков услуг IPVPN.

> [!NOTE]
>Для обеспечения высокой доступности корпорация Майкрософт устанавливает полностью избыточное параллельное соединение между парами MSEEs (5) и PE-MSEEs (4). Полностью избыточный параллельный сетевой путь также поощряется между сетью клиентов и парой PE-CEs. Для получения дополнительной информации о [Designing for high availability with ExpressRoute][HA] высокой доступности, см.
>
>

Ниже приведены логические шаги, в устранении неполадок ExpressRoute цепи:

* [Проверка подготовки и состояния контуров](#verify-circuit-provisioning-and-state)
  
* [Проверка настройки пиринга](#validate-peering-configuration)
  
* [Проверка ARP](#validate-arp)
  
* [Проверка BGP и маршрутов MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Подтвердите транспортный поток](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Проверка подготовки и состояния контуров
Подготовка схемы ExpressRoute устанавливает избыточное соединение уровня 2 между CEs/PE-MSEEs (2)/(4) и MSEEs (5). Дополнительные сведения о создании, изменении, подготовке и проверке канала ExpressRoute см. в статье [Создание и изменение канала ExpressRoute][CreateCircuit].

>[!TIP]
>Ключ службы однозначно идентифицирует канал ExpressRoute. Если вам нужна помощь от корпорации Майкрософт или партнера ExpressRoute для устранения неполадок в связи с проблемой ExpressRoute, предоставьте ключ службы для легкой идентификации цепи.
>
>

### <a name="verification-via-the-azure-portal"></a>Проверка на портале Azure
На портале Azure откройте лезвие цепи ExpressRoute. В ![3][3] разделе лезвия предметы первой необходимости ExpressRoute указаны в следующем скриншоте:

![4][4]    

Параметр *Состояние цепи* в разделе основных сведений об ExpressRoute указывает состояние канала на стороне Майкрософт. Параметр *Состояние поставщика* указывает, был ли канал *подготовлен* на стороне поставщика услуг. 

Для работы канала ExpressRoute параметр *Состояние цепи* должен иметь значение *Включено*, а параметр *Состояние поставщика* — значение *Подготовлено*.

> [!NOTE]
> После настройки схемы ExpressRoute, если *статус цепи* не включен в состояние, обратитесь в [службу поддержки Майкрософт.][Support] С другой стороны, если *статус поставщика* не имеет статуса, обратитесь к поставщику услуг.
>
>

### <a name="verification-via-powershell"></a>Проверка с помощью PowerShell
Чтобы получить список всех каналов ExpressRoute в группе ресурсов, используйте следующую команду:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Если вы ищете название группы ресурсов, вы можете получить его, перечислив все группы ресурсов в подписке, используя команду *Get-AzResourceGroup*
>


Чтобы выбрать конкретный канал ExpressRoute в группе ресурсов, используйте следующую команду:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Пример ответа:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Чтобы подтвердить работоспособность канала ExpressRoute, обратите особое внимание на следующие поля:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> После настройки схемы ExpressRoute, если *статус цепи* не включен в состояние, обратитесь в [службу поддержки Майкрософт.][Support] С другой стороны, если *статус поставщика* не имеет статуса, обратитесь к поставщику услуг.
>
>

## <a name="validate-peering-configuration"></a>Проверка настройки пиринга
После того, как поставщик услуг завершил подготовку схемы ExpressRoute, несколько конфигураций маршрутирования на основе eBGP могут быть созданы по схеме ExpressRoute между CEs/MSEE-PEs (2)/(4) и MSEEs (5). Каждая схема ExpressRoute может иметь: приватный пиринг Azure (трафик на частные виртуальные сети в Azure) и/или вглядывание Microsoft (трафик в общедоступные конечные точки PaaS и SaaS). Дополнительные сведения о создании и изменении настроек маршрутизации см. в статье [Создание и изменение маршрутизации для канала ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Проверка на портале Azure

> [!NOTE]
> В модели подключения IPVPN поставщики услуг справляют с ответственностью за настройку пирингов (сектор 3-го уровня). В такой модели, после того, как поставщик услуг настроил пиринг и если вглядывание пусто в портале, попробуйте обновить конфигурацию контура с помощью кнопки обновления на портале. Эта операция вытянет текущую конфигурацию маршрутизаний из вашей цепи. 
>

На портале Azure статус заверестьи схемы ExpressRoute можно проверить по лезвию схемы ExpressRoute. В ![3][3] разделе лезвия, ExpressRoute пиринги будут перечислены как показано на следующем скриншоте:

![5][5]

В предыдущем примере, как отмечено, подготовлено приватное пиринг Azure, в то время как публичные и недосматриваемые конспираты Azure и Microsoft не подготовлены. Успешно подготовленный контекст пиринга также будет иметь в списке первичные и вторичные подсети по точкам. Подсети /30 используются для IP-адреса интерфейса МСП и CE/PE-MSEEs. Для подготовленных пирингов в списке также указывается, кто в последний раз изменял конфигурацию. 

> [!NOTE]
> Если включение пиринга не удается, проверьте, совпадают ли назначенные первичные и вторичные подсети с конфигурацией на связанном CE/PE-MSEE. Также проверьте, используются ли на MSEEs правильные *VlanId,* *AzureASN*и *PeerASN* и используются ли эти значения к картам, используемым на связанных CE/PE-MSEE. При выборе хэширования MD5 общий ключ должен быть одинаковым на паре MSEE и PE-MSEE/CE. Ранее настроенный общий ключ не будет отображаться по соображениям безопасности. Если вам нужно изменить любую из этих конфигураций на маршрутизаторе MSEE, обратитесь к [Create и изменять маршрутизирование для схемы ExpressRoute.][CreatePeering]  
>

> [!NOTE]
> На подсетевом /30, назначенном для интерфейса, корпорация Майкрософт выберет второй пригодный для упокоительный IP-адрес подсети для интерфейса MSEE. Поэтому убедитесь, что первый пригодный к упомянуетесь IP-адрес подсети был назначен на заглянувом CE/PE-MSEE.
>


### <a name="verification-via-powershell"></a>Проверка с помощью PowerShell
Чтобы получить дополнительные сведения о конфигурации частного пиринга Azure, используйте следующие команды:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Пример ответа в случае успешной настройки частного пиринга:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Если контекст пиринга включен, в списке отобразятся первичные и вторичные префиксы адресов. Подсети /30 используются для IP-адреса интерфейса МСП и CE/PE-MSEEs.

Чтобы получить дополнительные сведения о настройке общедоступного пиринга Azure, используйте следующие команды:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Чтобы получить дополнительные сведения о настройке пиринга Майкрософт, используйте следующие команды:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Если пиринг не настроен, отобразится сообщение об ошибке. Пример ответа, если указанный пиринг (в этом примере общедоступный пиринг Azure) не настроен в канале.

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Если включение пиринга не удается, проверьте, совпадают ли назначенные первичные и вторичные подсети с конфигурацией на связанном CE/PE-MSEE. Также проверьте, используются ли на MSEEs правильные *VlanId,* *AzureASN*и *PeerASN* и используются ли эти значения к картам, используемым на связанных CE/PE-MSEE. При выборе хэширования MD5 общий ключ должен быть одинаковым на паре MSEE и PE-MSEE/CE. Ранее настроенный общий ключ не будет отображаться по соображениям безопасности. Если вам нужно изменить любую из этих конфигураций на маршрутизаторе MSEE, обратитесь к [Create и изменять маршрутизирование для схемы ExpressRoute.][CreatePeering]  
>
>

> [!NOTE]
> На подсетевом /30, назначенном для интерфейса, корпорация Майкрософт выберет второй пригодный для упокоительный IP-адрес подсети для интерфейса MSEE. Поэтому убедитесь, что первый пригодный к упомянуетесь IP-адрес подсети был назначен на заглянувом CE/PE-MSEE.
>

## <a name="validate-arp"></a>Проверка ARP

Таблица ARP предоставляет отображение IP-адреса и MAC-адреса для определенного пиринга. Таблица ARP для пиринга канала ExpressRoute содержит следующие сведения о каждом интерфейсе (первичном и вторичном).
* Сопоставление локального IP-адреса интерфейса маршрутизатора с MAC-адресом.
* Сопоставление IP-адреса интерфейса маршрутизатора ExpressRoute с MAC-адресом.
* Возраст картографических таблиц ARP может помочь проверить конфигурацию слоя 2 и устранить основные проблемы подключения 2-го слоя.


В типе [модели развертывания диспетчера ресурсов][ARP] смотрите сведения о том, как просматривать таблицу ARP вонючих ExpressRoute и как использовать информацию для устранения проблем ы устранением проблемы подключения 2-го уровня.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Проверка BGP и маршрутов MSEE

Чтобы получить таблицу маршрутов из MSEE на пути *Первоначального общества* для контекста *частной* маршрутики, используйте следующую команду:

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

Пример ответа:

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> Если состояние eBGP между MSEE и CE/PE-MSEE находится в активном или простое, проверьте, соответствуют ли назначенные первичные и вторичные одноранговые подсети конфигурации на связанном CE/PE-MSEE. Также проверьте, используются ли на MSEEs правильные *VlanId,* *AzureAsn*и *PeerAsn* и используются ли эти значения к картам, используемым на связанном PE-MSEE/CE. При выборе хэширования MD5 общий ключ должен быть одинаковым на паре MSEE и CE/PE-MSEE. Если вам нужно изменить любую из этих конфигураций на маршрутизаторе MSEE, обратитесь к [Create и изменять маршрутизирование для схемы ExpressRoute.][CreatePeering]
>


> [!NOTE]
> Если некоторые направления недоступны в течение пиринга, проверьте таблицу маршрутов МСП на соответствующий контекст пиринга. Если соответствующая префикс (может быть NATed IP) присутствует в таблице маршрутов, то проверьте, есть ли брандмауэры/NSG/ACL на пути, который блокирует трафик.
>


Следующий пример показывает ответ команды для пиринга, который не существует:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>Подтвердите транспортный поток
Для получения сводной статистики трафика (переданных и полученных байтов) по первичному и вторичному пути для контекста пиринга используйте следующую команду:

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

Пример результата выполнения команды:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Пример результата выполнения команды при отсутствующем пиринге:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>Next Steps
Дополнительные сведения доступны в следующих источниках:

- [Служба технической поддержки Майкрософт][Support]
- [Создание и изменение канала ExpressRoute][CreateCircuit]
- [Создание и изменение маршрутизации для канала ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Логическое подключение ExpressRoute"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Значок "Все ресурсы""
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Значок "Обзор""
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Снимок экран раздела с основными сведениями об ExpressRoute"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Снимок экран раздела с основными сведениями об ExpressRoute"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





