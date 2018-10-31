---
title: Взаимодействие между ExpressRoute, VPN типа "сеть — сеть" и пирингом виртуальных сетей. Сведения о конфигурации взаимодействия компонентов подключения к серверной части в Azure | Документация Майкрософт
description: Эта страница содержит сведения о тестовой конфигурации, которая используется для анализа взаимодействия возможностей ExpressRoute, VPN типа "сеть — сеть" и пиринга виртуальных сетей.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: d94900b764331c6fff0e0384e6edbebc88ac938b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947099"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-configuration-details"></a>Взаимодействие ExpressRoute, VPN типа "сеть — сеть" и пиринга виртуальных сетей. Сведения о конфигурации теста

В этой статье мы разберем сведения о тестовой конфигурации. Сама тестовая конфигурация описана в [этой статье][Setup]. 

##<a name="spoke-vnet-connectivity-using-vnet-peering"></a>Подключение к периферийной виртуальной сети с использованием пиринга

Ниже приведен снимок экрана портала Azure, где показаны сведения о пиринге для периферийной виртуальной сети. Пошаговое руководство по настройке пиринговой связи между двумя виртуальными сетями см. в статье [Создание, изменение и удаление пиринга в виртуальной сети][VNet-Config]. Если требуется, чтобы периферийная виртуальная сеть использовала шлюзы, подключенные к центральной виртуальной сети, необходимо установить флажок *Use remote gateways* (Использовать удаленные шлюзы).

[![1]][1]

Ниже приведен снимок экрана портала Azure, где показаны сведения о пиринге для центральной виртуальной сети. Если вы хотите, чтобы периферийная виртуальная сеть могла использовать шлюзы центральной виртуальной сети, установите флажок *Use remote gateways* (Использовать удаленные шлюзы).

[![2]][2]

##<a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Подключение к виртуальной сети филиала с помощью VPN типа "сеть — сеть"

Подключение между центральной виртуальной сетью и виртуальными сетями филиалов с использованием VPN типа "сеть — сеть" настраивается с помощью VPN-шлюзов. По умолчанию для шлюза ExpressRoute и VPN-шлюза настраивается частный номер ASN 65515. Для VPN-шлюза можно изменить значение ASN. В тестовой конфигурации, как показано на следующем снимке экрана портала Azure, значение ASN в VPN-шлюза виртуальной сети филиала изменено на 65516, чтобы включить маршрутизацию eBGP между центральной виртуальной сетью и виртуальными сетями филиалов.


[![3]][3]


##<a name="location-1-on-premises-connectivity-using-expressroute-and-site-to-site-vpn"></a>Подключение к локальному расположению 1 через ExpressRoute и VPN типа "сеть — сеть"

###<a name="expressroute1-configuration-details"></a>Сведения о конфигурации ExpressRoute1

На следующем снимке экрана портала показана конфигурация канала ExpressRoute в регионе Azure 1 в отношении маршрутизаторов CE локального расположения 1.

[![4]][4]

На следующем снимке экрана показана конфигурация подключения между каналом ExpressRoute1 и центральной виртуальной сетью.

[![5]][5]

Ниже приведена основная конфигурация маршрутизатора CE (маршрутизаторы Cisco ASR1000 используются в качестве маршрутизаторов CE в тестовой конфигурации), связанная с частным пирингом ExpressRoute. Если и VPN типа "сеть — сеть", и канал ExpressRoute настроены параллельно для подключения локальной сети к Azure, по умолчанию Azure использует канал ExpressRoute. Чтобы избежать асимметричной маршрутизации, локальной сети следует также использовать ExpressRoute вместо VPN типа "сеть — сеть" для маршрутов, полученных через ExpressRoute и VPN типа "сеть — сеть". Это достигается в следующей конфигурации с помощью атрибута локального предпочтения BGP. 

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

###<a name="site-to-site-vpn-configuration-details"></a>Сведения о конфигурации VPN типа "сеть — сеть"

Ниже приведена основная конфигурация маршрутизатора CE, которая связана с VPN типа "сеть — сеть":

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

##<a name="location-2-on-premises-connectivity-using-expressroute"></a>Подключение к локальному расположению 2 через ExpressRoute

Второй канал ExpressRoute, расположенный близко к локальному расположению 2, подключает расположение 2 к центральной виртуальной сети. На следующем снимке экрана портала показана вторая конфигурация ExpressRoute.

[![6]][6]

На следующем снимке экрана показана конфигурация подключения между вторым каналом ExpressRoute и центральной виртуальной сетью.

[![7]][7]

ExpressRoute1 соединяет центральную виртуальную сеть и локальное расположение 1 с удаленной виртуальной сетью в другом регионе Azure.

[![8]][8]

## <a name="further-reading"></a>Дополнительные материалы

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Параллельное использование подключения ExpressRoute и VPN-подключения типа "сеть — сеть"

#### <a name="site-to-site-vpn-over-expressroute"></a>VPN-подключение типа "сеть — сеть" через ExpressRoute

VPN-подключение типа "сеть — сеть" можно настроить через пиринг Microsoft ExpressRoute, чтобы организовать закрытый обмен данными между локальной сетью и виртуальными сетями Azure с гарантией конфиденциальности, защиты от повтора, подлинности и целостности. Дополнительные сведения о настройке VPN-подключения IPSec типа "сеть — сеть" в туннельном режиме через пиринг Microsoft ExpressRoute см. [в этой статье][S2S-Over-ExR]. 

Основным ограничением конфигурации с VPN-подключением типа "сеть — сеть" через пиринг Майкрософт является пропускная способность. Пропускная способность туннеля IPSec ограничена емкостью VPN-шлюза. Пропускная способность шлюза VPN всегда меньше пропускной способности ExpressRoute. В таких ситуациях можно оптимизировать использование пропускной способности ExpressRoute, настроив туннель IPSec для трафика, требующего высокого уровня безопасности, и частный пиринг для всего остального трафика.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN-подключение типа "сеть — сеть" в качестве пути отработки отказа для ExpressRoute.
ExpressRoute предоставляется в виде пары каналов, что обеспечивает избыточность и высокий уровень доступности. Вы можете настроить географически избыточное подключение ExpressRoute в разных регионах Azure. Как и в нашей тестовой конфигурации, если для подключения ExpressRoute нужен переход на другой ресурс в пределах определенного региона Azure, его можно создать с помощью VPN-подключения типа "сеть — сеть". Если одни и те же префиксы объявляются через ExpressRoute и VPN-подключение типа "сеть — сеть", Azure отдает предпочтение ExpressRoute. Чтобы избежать асимметричной маршрутизации между ExpressRoute и VPN-подключением типа "сеть — сеть", в локальной сети следует также настроить конфигурацию с более высоким приоритетом ExpressRoute относительно VPN-подключения типа "сеть — сеть".

Дополнительные сведения о настройке параллельных подключений ExpressRoute и VPN типа "сеть — сеть" см. в статье [Настройка параллельных подключений "сеть — сеть" и ExpressRoute с помощью PowerShell][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Расширение подключения серверной части к периферийным виртуальным сетям и филиалам

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Подключение к периферийной виртуальной сети с использованием пиринга

Звездообразная архитектура виртуальных сетей нашла широкое применение. Главной является виртуальная сеть в Azure, которая выступает в качестве центральной точки подключения периферийных виртуальных сетей к локальной сети. "Периферийными" называются виртуальные сети, которые устанавливают пиринг с центральной сетью и могут использоваться для изоляции рабочих нагрузок. Трафик передается между локальным центром обработки данных и центральной сетью через подключение ExpressRoute и (или) VPN. Дополнительные сведения о звездообразной архитектуре см. в [этой статье][Hub-n-Spoke].

Пиринг виртуальных сетей в пределах региона позволяет периферийным виртуальным сетям использовать шлюзы центральной виртуальной сети (шлюз VPN или ExpressRoute) для подключения к удаленным сетям.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Подключение к виртуальной сети филиала с помощью VPN типа "сеть — сеть"

Виртуальные сети филиалов (расположенные в разных регионах) могут взаимодействовать с локальными сетями через центральную виртуальную сеть. Стандартным решением в Azure для такой конфигурации является VPN-подключение типа "сеть — сеть". Для маршрутизации в центральной сети также можно использовать виртуальные сетевые модули.

Сведения о настройке VPN-шлюзов см. в [этой статье][VPN]. Сведения о развертывании высокодоступных виртуальных сетевых модулей (NVA) см. в [этой статье][Deploy-NVA].

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о плоскости управления тестовой конфигурации и представлениях разных виртуальных сетей и локальных виртуальных сетей см. в [этой статье][Control-Analysis].

Дополнительные сведения о плоскости данных тестовой конфигурации и представлениях функций мониторинга сети Azure см. в [этой статье][Data-Analysis].

Чтобы узнать, сколько каналов ExpressRoute можно подключить к шлюзу ExpressRoute и сколько шлюзов ExpressRoute можно подключить к каналу ExpressRoute, а также изучить другие ограничения масштабирования ExpressRoute, см. [вопросы и ответы об ExpressRoute][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "Пиринг для периферийной виртуальной сети"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Пиринг для центральной виртуальной сети"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "Конфигурация VPN-шлюза виртуальной сети филиала"
[4]: ./media/backend-interoperability/ExR1.png "Конфигурация ExpressRoute1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Конфигурация подключения ExpressRoute1 к шлюзу центральной виртуальной сети ExpressRoute"
[6]: ./media/backend-interoperability/ExR2.png "Конфигурация ExpressRoute2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Конфигурация подключения ExpressRoute2 к шлюзу центральной виртуальной сети ExpressRoute"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Конфигурация подключения ExpressRoute2 к шлюзу удаленной виртуальной сети ExpressRoute"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




