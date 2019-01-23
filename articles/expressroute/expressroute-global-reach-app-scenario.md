---
title: Сценарий приложения Azure ExpressRoute Global Reach | Документация Майкрософт
description: На этой странице описывается пример сценария для приложения Global Reach.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: rambala
ms.openlocfilehash: 5174baae40d7f14720affe90f702c024541a5745
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54333086"
---
# <a name="expressroute-global-reach-application-scenario"></a>Сценарий приложения ExpressRoute Global Reach

Дополнительные сведения об ExpressRoute Global Reach см. в статье [ExpressRoute Global Reach (Предварительная версия)][Global Reach]. В этой статье мы подробно рассмотрим сценарий приложения, сравним решение ExpressRoute Global Reach с несколькими альтернативами, настроим Global Reach для нашего тестового сценария и проверим подключения. 

##<a name="application-scenario"></a>Сценарий приложения

Компания Fabrikam Inc. активно работает в восточной части США и имеет развернутые службы Azure в этом регионе. Fabrikam использует ExpressRoute для связи между серверными компонентами, размещенными в локальной сети и в Azure. Компания Contoso Ltd. работает в западной части США и имеет развернутые службы Azure в этом регионе. Contoso использует ExpressRoute для связи между серверными компонентами, размещенными в локальной сети и в Azure.  

Компания Fabrikam Inc. приобретает компанию Contoso Ltd. После этого слияния компания Fabrikam намерена объединить обе сети. Этот сценарий показан на следующей схеме:

 [![1]][1]

Пунктирные стрелки в средней части схемы на рисунке выше обозначают необходимые сетевые соединения. Ниже показана таблица маршрутов первичного частного пиринга ExpressRoute в компании Contoso Ltd. до слияния.

[![2]][2]

Ниже показана таблица маршрутов первичного частного пиринга ExpressRoute в компании Fabrikam Inc. до слияния.

[![3]][3]

## <a name="traditional-solutions"></a>Традиционные решения

### <a name="option-1-interconnect-on-premises-networks"></a>Вариант 1. Объединение локальных сетей

Этот вариант показан на следующей схеме: Как вы видите, две локальные сети можно объединить с помощью VPN "сеть — сеть" или другого широкополосного подключения и управлять маршрутизацией между двумя предприятиями. 

[![4]][4]

Этот вариант имеет следующие недостатки:

- Принудительное применение неоптимального маршрута для межрегиональных взаимодействий между службами Azure.
- Вы отказываетесь от преимуществ высокого уровня доступности, который обеспечивает магистральная сеть Майкрософт для обмена данными между регионами.
- На вас ложатся все обязательства по маршрутизации трафика между регионами.

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>Вариант 2. Перекрестное подключение ExpressRoute и объединение локальных сетей

Этот вариант показан на следующей схеме:

[![5]][5]

Как показано на рисунке выше, вы можете дополнить предыдущий вариант подключением каналов ExpressRoute к виртуальным сетям в другом регионе. Региональное перекрестное подключение виртуальных сетей к каналам ExpressRoute будет поддерживать следующие взаимодействия:

- между виртуальными сетями в регионах "Западная часть США" и "Восточная часть США" и локальными сетями Fabrikam и Contoso, соответственно;
- между виртуальной сетью в регионе "Западная часть США" и виртуальной сетью в регионе "Восточная часть США".

Чтобы две локальные сети могли обмениваться данными, в этой схеме по-прежнему требуется подключение между ними.

Этот вариант позволяет направить трафик Azure между регионами развертывания через магистральную сеть Майкрософт, а взаимодействие между локальными сетями выполнять через внешнюю сеть. Основным недостатком этого решения является необходимость создавать несколько перекрестных подключений между регионами, что усложняет обслуживание и устранение неполадок. Кроме того, при таком варианте вы не сможете воспользоваться преимуществами высокого уровня доступности магистральной сети Майкрософт для обмена данными между локальными сетями.

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>Вариант 3. Пиринг виртуальных сетей с объединением локальных сетей

Этот вариант показан на следующей схеме: В этом варианте для обмена данными между виртуальными сетями в разных регионах используется пиринг виртуальных сетей. Как видно на схеме, этот вариант будет неполным, так как он не поддерживает обмен данными между виртуальной сетью и локальной сетью в другом регионе (обозначен двумя пунктирными стрелками в середине схемы). Для взаимодействия между облаком и локальной сетью в разных регионах будет применяться подключение между локальными сетями (как в варианте 1) или перекрестное подключение ExpressRoute (как в варианте 2).

[![6]][6]

Этот вариант обеспечивает оптимальную маршрутизацию для связи между виртуальными сетями в разных регионах. Но он не справится с поставленной задачей, если у любой из компаний Fabrikam и Contoso есть более сложные развертывания Azure, например звездообразная схема виртуальных сетей. Этот вариант, как и оба предыдущих, не позволяет воспользоваться преимуществами высокого уровня доступности магистральной сети Майкрософт для обмена данными между локальными сетями.

## <a name="global-reach"></a>Global Reach

ExpressRoute Global Reach объединяет частный пиринг нескольких каналов ExpressRoute, как показано на следующей схеме:

[![7]][7]

Настроив Global Reach между двумя каналами ExpressRoute, вы сразу получите частное подключение между двумя локальными сетями и развернутыми службами в двух регионах Azure. Таким образом, Global Reach удовлетворяет всем требованиям к обмену данными (которые обозначены пунктирными линиями на первом рисунке этой статьи) через магистральную сеть Майкрософт.

### <a name="configure-global-reach"></a>Настройка Global Reach

Сведения о настройке ExpressRoute Global Reach см. в статье [Настройка ExpressRoute Global Reach (предварительная версия)][Configure Global Reach]. 

Так как компании Fabrikam Inc. и Contoso Ltd. подключались к Azure как разные компании, их каналы ExpressRoute находятся в двух разных подписках Azure. Чтобы настроить Global Reach между подписками, потребуется создать авторизацию в канале ExpressRoute, принадлежащем компании Contoso Ltd., и передать ее компании Fabrikam Inc. Канал ExpressRoute.


Чтобы создать авторизацию для канала ExpressRoute компании Contoso, войдите в учетную запись Azure компании Contoso и выберите соответствующую подписку (если их несколько). Ниже приведены команды PowerShell для этой процедуры.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
Ниже перечислены шаги по созданию авторизации канала ExpressRoute.

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

В выходных данных командлета Set-AzureRmExpressRouteCircuit указан канал ExpressRouteCircuit. Вам потребуются идентификатор частного пиринга и ключ авторизации, которые указаны в конце выходных данных. Вот пример выходных данных, которые возвращает PowerShell (фрагмент):

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

Получив идентификатор пиринга и ключ авторизации, вы сможете создать Global Reach в канале ExpressRoute компании Fabrikam. Войдите в учетную запись Azure компании Fabrikam. Если у вас есть несколько подписок, выберите нужную.

Global Reach создает избыточный набор подключений "точка — точка" в виде двух пар MSEE. Для двух подключений "точка — точка" потребуется сеть адресов с префиксом /29 (для этого примера мы воспользуемся сетью 192.168.11.64/29). Используйте следующие команды, чтобы создать подключение Global Reach:

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

После запуска указанных выше команд потребуется несколько минут на создание избыточных подключений Global Reach.

### <a name="verify-global-reach"></a>Проверка Global Reach

В следующей таблице представлены маршруты первичного частного пиринга ExpressRoute в компании Contoso Ltd. после настройки Global Reach.

[![8]][8]

В следующей таблице представлены маршруты первичного частного пиринга ExpressRoute в компании Fabrikam Inc. после настройки Global Reach.

[![9]][9]

В приведенных выше таблицах представлены все префиксы ожидаемых назначений (NETWORK) и соответствующие им адреса следующего прыжка (NEXT HOP).

На снимке экрана выше вы видите колонку "Получить таблицу маршрутизации", которую можно открыть на портале Azure из раздела частного пиринга для канала ExpressRoute. Эту же таблицу маршрутов ExpressRoute можно получить с помощью следующей команды PowerShell:

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

Чтобы просмотреть таблицу маршрутов для вторичного MSEE, замените ключевое слово primary на secondary в приведенной выше команде.

Теперь давайте проверим подключение плоскости данных, выполнив команду ping из разных сетей. Следующие три проверки связи подтверждают наличие следующих подключений плоскости данных из локальной сети компании Fabrikam: к локальной сети Contoso, к виртуальной сети Contoso в Azure и к виртуальной сети Fabrikam в Azure.


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


Следующие две проверки связи подтверждают наличие следующих подключений плоскости данных из локальной сети компании Fabrikam: к виртуальной сети Contoso в Azure и к виртуальной сети Fabrikam в Azure.

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

Следующая проверка связи проверяет подключение плоскости данных из виртуальной сети Fabrikam в Azure к виртуальной сети Contoso в Azure.

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>Оптимизация для трафика, чувствительного к задержкам

Global Reach направляет трафик через устройства Microsoft Edge. Для конкретного сценария, который рассматривается в этой статье, можно улучшить маршрутизацию между двумя виртуальными сетями, создав между ними пиринговую связь. Аналогичным образом, маршрутизацию между двумя локальными сетями можно улучшить, создав более прямое подключение между сетями через поставщика услуг связи. В обоих случаях Global Reach останется резервным вариантом для маршрутизации в случае сбоев. 

## <a name="next-steps"></a>Дополнительная информация

Global Reach развертывается постепенно для разных стран. Информацию о доступности Global Reach в нужных вам странах вы найдете в статье [ExpressRoute Global Reach (Предварительная версия)][Global Reach].

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "Сценарий приложения"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "Таблица маршрутов ExpressRoute компании Contoso до слияния"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "Таблица маршрутов ExpressRoute компании Fabrikam до слияния"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "Объединение локальных сетей"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "Перекрестное подключение ExpressRoute"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "Пиринговая связь между виртуальными сетями"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "Global Reach"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "Таблица маршрутов ExpressRoute компании Contoso с применением Global Reach"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "Таблица маршрутов ExpressRoute компании Fabrikam с применением Global Reach"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





