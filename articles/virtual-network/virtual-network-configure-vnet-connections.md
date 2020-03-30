---
title: Настройка и проверка виртуальных сетевых или VPN-соединений
description: Пошагзатое руководство по настройке и проверке различных развертываний VPN azure и виртуальных сетей
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099060"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Настройка и проверка виртуальных сетевых или VPN-соединений

Это пошаговое руководство обеспечивает пошаговое руководство по настройке и проверке различных развертываний Azure VPN и виртуальных сетевых сетей. Сценарии включают маршрутизирование транзита, сетевые соединения, протокол пограничных шлюзов (BGP), многоузловые соединения и соединения точки к месту.

Vpn шлюзы Azure обеспечивают гибкость в организации практически любого вида подключенной виртуальной сетевой топологии в Azure. Например, можно подключить виртуальные сети:

- В разных регионах.
- Между виртуальными типами сетей (Azure Resource Manager против классических).
- В Azure или в рамках предварительной гибридной среды.
- В разных подписках. 

## <a name="network-to-network-vpn-connection"></a>VPN-соединение от сети к сети

Подключение виртуальной сети к другой виртуальной сети (сетевой сети) с помощью VPN аналогично подключению виртуальной сети к месту расположения. Оба типа подключения используют VPN шлюз для обеспечения безопасного туннеля через IPsec и IKE. Виртуальные сети могут относиться к одному или разным регионам и к одной или разным подпискам.

![Сетевое подключение к сети с IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Если ваши виртуальные сети находятся в одном регионе, вы можете рассмотреть возможность их подключения с помощью виртуальной сети пиринга. Виртуальная сеть пиринга не использует VPN шлюз. Это увеличивает пропускную силу и уменьшает задержку. Чтобы настроить виртуальное подключение к пирингу сети, выберите **настройку и проверку VNet Peering.**

Если ваши виртуальные сети были созданы с помощью модели развертывания Ресурсов Azure, выберите **настройку и проверку менеджера ресурсов VNet на подключение менеджера ресурсов VNet** для настройки VPN-соединения.

Если одна из виртуальных сетей была создана с помощью классической модели развертывания Azure, а другая — через диспетчер ресурсов, **выберите настроить и проверить классическую VNet на подключение менеджера ресурсов VNet** для настройки VPN-соединения.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Настройка виртуальной сети для двух виртуальных сетей в одном регионе

Перед тем, как приступить к реализации и настройке виртуальной сети Azure, убедитесь, что вы отвечаете следующим предпосылкам:

* Пиринговые виртуальные сети должны находиться в одном регионе Azure.
* Заглянул виртуальных сетей должны иметь IP-адрес пространства, которые не пересекаются.
* Пиринговая связь устанавливается между двумя виртуальными сетями. Там нет производных транзитных отношений между пирингами. Например, если VNetA заглянул с VNetB, а VNetB заглянул с VNetC, VNetA *не* заглядывается с VNetC.

Когда вы отвечаете требованиям, вы можете следовать [учебник: Подключите виртуальные сети с виртуальной сети пиринг с помощью портала Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) для создания и настройки пиринга.

Чтобы проверить конфигурацию пиринга, используйте следующий метод:

1. Войти на [портал Azure,](https://portal.azure.com/) используя учетную запись с необходимыми [ролями и разрешениями.](virtual-network-manage-peering.md#permissions)
2. В верхней части окна портала в поле с текстом **Поиск ресурсов** введите **виртуальные сети**. Когда в результатах поиска появится пункт **Виртуальные сети**, выберите его.
3. В появляется лезвие **виртуальных сетей,** выберите виртуальную сеть, для которой вы хотите создать пиринг.
4. В панели, которая отображается для виртуальной сети, выберите **Peerings** в разделе **Настройки.**
5. Выберите одноранговой и просмотрите результаты конфигурации.

![Выбор для проверки конфигурации пиринга виртуальной сети](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Для Azure PowerShell запустите команду [Get-AzureRmVirtualNetworkPeering,](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) чтобы получить виртуальную сеть пиринг. Ниже приведен пример:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Подключение виртуальной сети менеджера ресурсов к другой виртуальной сети resource Manager

Вы можете настроить соединение из одной виртуальной сети Resource Manager на другую виртуальную сеть Resource Manager напрямую. Или вы можете настроить соединение с помощью IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Налаживание VPN-соединения между виртуальными сетями Resource Manager

Чтобы настроить соединение между виртуальными сетями Resource Manager без IPsec, см. [Наверстывайте сетевое подключение VPN-шлюза с помощью портала Azure.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)

Чтобы настроить соединение с IPsec между двумя виртуальными сетями Resource Manager, выполните шаги от 1 до 5 в создании подключения к сайту на [портале Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) для каждой виртуальной сети.

> [!Note]
> Эти шаги работают только для виртуальных сетей в той же подписке. Если ваши виртуальные сети находятся в разных подписках, вы должны использовать PowerShell, чтобы сделать соединение. См. статью, посвященную использованию [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Проверка VPN-соединения между виртуальными сетями Resource Manager

![Классическое виртуальное сетевое подключение к виртуальной сети Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Чтобы проверить правильное подключение VPN, следуйте этим инструкциям.

> [!Note] 
> Числа после виртуальных сетевых компонентов на этих этапах соответствуют числам на предыдущей диаграмме.

1. Убедитесь, что в подключенных виртуальных сетях нет перекрывающихся адресных пространств.
2. Убедитесь, что диапазон адресов виртуальной сети Azure Resource Manager (1) точно определен в экземпляре **объекта соединения** (4).
3. Убедитесь, что диапазон адресов виртуальной сети Azure Resource Manager (6) точно определен в экземпляре **объекта соединения** (3).
4. Убедитесь, что предварительно общие ключи совпадают с объектами соединения.
5. Убедитесь, что виртуальный сетевой шлюз Azure Resource Manager VIP (2) точно определен в экземпляре **объекта соединения** (4).
6. Убедитесь, что виртуальный сетевой шлюз Azure Resource Manager VIP (5) точно определен в экземпляре **объекта соединения** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Подключение классической виртуальной сети к виртуальной сети Resource Manager

Можно создать соединение между виртуальными сетями, которые находятся в разных подписях и в разных регионах. Вы также можете подключить виртуальные сети, которые уже имеют соединения с наземными сетями, если вы настроили тип шлюза как маршрутный.

Чтобы настроить соединение между классической виртуальной сетью и виртуальной сетью Resource Manager, [см. Connect виртуальные сети из различных моделей развертывания с помощью портала Azure.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)

![Классическое виртуальное сетевое подключение к виртуальной сети Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Чтобы проверить конфигурацию при подключении классической виртуальной сети к виртуальной сети Azure Resource Manager, следуйте этим инструкциям.

> [!Note] 
> Числа после виртуальных сетевых компонентов на этих этапах соответствуют числам на предыдущей диаграмме. 

1. Убедитесь, что в подключенных виртуальных сетях нет перекрывающихся адресных пространств.
2. Убедитесь, что диапазон адресов для виртуальной сети Azure Resource Manager (6) точно определен в классическом определении локальной сети (3).
3. Убедитесь, что диапазон адресов для классической виртуальной сети (1) точно определен в **экземпляре объекта подключения** к ресурсам Azure Manager (4).
4. Убедитесь, что классический виртуальный сетевой шлюз VIP (2) точно определен в **экземпляре объекта подключения** к ресурсам Azure Resource Manager (4).
5. Убедитесь, что виртуальный сетевой шлюз Azure Resource Manager (5) точно определен в классическом экземпляре **определения локальной сети** (3).
6. Убедитесь, что предварительно общие ключи совпадают в обеих подключенных виртуальных сетях:
   - Классическая виртуальная сеть: **Определение локальной сети** (3)
   - Виртуальная сеть Azure Resource Manager: **объект подключения** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Создание VPN-соединения от точки к сайту

Конфигурация «точка к сайту»*(P2S* на следующей диаграмме) позволяет создать безопасное соединение с отдельного клиентского компьютера в виртуальную сеть. Соединения от точки к месту полезны, когда вы хотите подключиться к виртуальной сети из удаленного местоположения, например, из дома или на конференции. Они также полезны, когда у вас есть только несколько клиентов, которые должны подключиться к виртуальной сети. 

VPN-соединение от точки к сайту инициируется с клиентского компьютера через родной клиент Windows VPN. Для проверки подлинности подключений клиентов используются сертификаты.

![Подключение типа "точка — сеть"](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Соединения от точки к месту не требуют VPN-устройства. Они создают VPN соединение по протоколу безопасного туннелирования розетки (SSTP). Подключение к виртуальной сети по месту от точки к месту можно с помощью различных инструментов развертывания и моделей развертывания:

* [Настройте подключение к виртуальной сети по месту от точки к сайту с помощью портала Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Настройте связь от точки к сайту с виртуальной сетью с помощью портала Azure (классический)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Настройте связь от точки к сайту с виртуальной сетью с помощью PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>Проверка подключения к месту от точки к сайту

В статье [Troubleshooting: Проблемы подключения от точки к сайту проходят](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) через общие проблемы с соединениями от точки к сайту.

## <a name="create-a-multisite-vpn-connection"></a>Создание мультиузлового VPN-соединения

Вы можете добавить подключение от сайта к сайту *(S2S* на следующей диаграмме) к виртуальной сети, которая уже имеет подключение от сайта к сайту, подключение к сайту или подключение от сети к сети. Этот вид соединения часто называют *многоузловой* конфигурацией. 

![Многоузловое соединение](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Сейчас Azure поддерживает две модели развертывания: классическую и с использованием Resource Manager. Эти две модели не полностью совместимы друг с другом. Для настройки многоузлового соединения с различными моделями см.

* [Добавьте подключение к сайту в виртуальную сеть с существующим vpn-шлюзом](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Добавьте подключение к сайту к сайту в виртуальную сеть с существующим VPN-шлюзом (классическим)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Шаги в этих статьях не применяются к конфигурациям соединения Azure ExpressRoute и сайта к сайту. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)

## <a name="configure-transit-routing"></a>Настройка транзитной маршрутики

Транзитная маршрутизирующая маршрутизм — это специфический сценарий маршрутизаний, в котором вы соединяете несколько сетей в топологии ромашки. Эта система передачи позволяет ресурсам в виртуальных сетях на обоих концах цепочки общаться друг с другом через виртуальные сети между ними. Без маршрутики транзита сети или устройства, заглядываемые через концентратор, не могут дотянуться друг с другом.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Настройка транзитной маршрутики в подключении точки к месту

Представьте себе сценарий, в котором вы хотите настроить VPN-соединение между VNetA и VNetB. Вы также хотите настроить VPN от точки к сайту для клиента для подключения к шлюзу VNetA. Затем необходимо включить транзитную маршрутизм для клиентов, которые будут подключены к VNetB, который проходит через VNetA. 

Этот сценарий поддерживается, когда BGP включен на VPN от сайта к сайту между VNetA и VNetB. Для получения дополнительной информации, [см.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Настройка транзитной маршрутики в подключении ExpressRoute

Azure ExpressRoute позволяет переносить локальные сети в облако Microsoft по выделенному закрытому соединению, которое обеспечивается поставщиком услуг подключения. ExpressRoute позволяет устанавливать подключения к облачным службам Майкрософт, таким как Microsoft Azure, Office 365 и Dynamics 365. См. дополнительные сведения об [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Частное подключение ExpressRoute к виртуальным сетям Azure](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Мы рекомендуем, чтобы, если VNetA и VNetB находятся в одном геополитическом регионе, вы [связываете обе виртуальные сети с схемой ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) вместо настройки маршрутики транзита. Если ваши виртуальные сети находятся в разных геополитических регионах, вы также можете связать их с вашей схемой непосредственно, если у вас есть [ExpressRoute Premium.](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) 

Если у вас есть ExpressRoute и сосуществование сайта на сайте, транзитная маршрутизм не поддерживается. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)

Если вы включили ExpressRoute для подключения локальных сетей к виртуальной сети Azure, можно включить вглярание между виртуальными сетями, где требуется транзитная маршрутизм. Чтобы позволить локальной сети подключиться к удаленной виртуальной сети, необходимо настроить [виртуальную сеть пиринга.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity) 

> [!Note]
> Виртуальная сеть пиринга доступна только для виртуальных сетей в том же регионе.

Чтобы проверить, настроили ли вы маршруттранзитную маршрутизм для виртуальной сети пиринга, следуйте следующим инструкциям:

1. Войти на [портал Azure,](https://portal.azure.com/) используя учетную запись с необходимыми [ролями и разрешениями.](virtual-network-manage-peering.md#permissions)
2. [Создайте пиринг между VNetA и VNetB,](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) как показано на предыдущей диаграмме. 
3. В панели, которая отображается для виртуальной сети, выберите **Peerings** в разделе **Настройки.**
4. Выберите просматриваемый просмотр. Затем выберите **Configuration,** чтобы проверить, что вы включили **Allow Gateway Transit** в сети VNetA, подключенной к схеме ExpressRoute, и **используйте удаленный шлюз** в удаленной сети VNetB, не подключенной к схеме ExpressRoute.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Настройка транзитной маршрутики в виртуальном сетевом пиринговом соединении

Если между виртуальными сетями установлена пиринговая связь, можно использовать шлюз в одной из этих сетей в качестве транзитного пункта при подключении к локальной сети. Для настройки транзитного маршрута в виртуальной сети пиринг, см [Сети к сети соединений](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> Транзит шлюза не поддерживается в пиринговых отношениях между виртуальными сетями, созданными с помощью различных моделей развертывания. Обе виртуальные сети в пиринговых отношениях должны быть созданы через Ресурсный менеджер для транзита шлюза для работы.

Чтобы проверить, настроили ли вы транзитный маршрут для виртуального вглядывания сети, следуйте следующим инструкциям:

1. Войти на [портал Azure,](https://portal.azure.com/) используя учетную запись с необходимыми [ролями и разрешениями.](virtual-network-manage-peering.md#permissions)
2. В верхней части окна портала в поле с текстом **Поиск ресурсов** введите **виртуальные сети**. Когда в результатах поиска появится пункт **Виртуальные сети**, выберите его.
3. В появляется лезвие **виртуальных сетей,** выберите виртуальную сеть, для которой необходимо проверить настройки вглядывания.
4. В панели, которая отображается для выбранной вами виртуальной сети, выберите **раздел «Peerings** in the **Settings».**
5. Выберите вглядываение, которое вы хотите просмотреть. Проверка того, что вы включили **Разрешить транзит шлюза** и **использовать удаленные шлюзы** в **конфигурации.**

![Выбор для проверки того, что вы настроили транзитный маршрут для виртуального пиринга сети](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Настройка транзитной маршрутики в сетевом подключении

Чтобы настроить маршрутизирование транзита между виртуальными сетями, необходимо включить BGP на всех промежуточных сетевых подключений с помощью модели развертывания диспетчера ресурсов и PowerShell. Для получения инструкций см. [Как настроить BGP на VPN шлюзах Azure с помощью PowerShell.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

Транзитный трафик через VPN шлюзы Azure возможен с помощью классической модели развертывания, но он опирается на статически определенные адресные пространства в файле конфигурации сети. BGP пока не поддерживается виртуальными сетями Azure и VPN-шлюзами с помощью классической модели развертывания. Без BGP, вручную определяя транзитные адресные пространства, подвержены ошибкам, и мы не рекомендуем его.

> [!Note]
> Вы настраиваете классические сетевые соединения с помощью классического портала Azure или с помощью файла конфигурации сети в классическом портале. Вы не можете создать или изменить классическую виртуальную сеть через модель развертывания ресурсов Azure Manager или портал Azure. Для получения дополнительной информации о маршрутизации транзита для классических виртуальных сетей, [см.](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Настройка транзитной маршрутики в подключении от сайта к месту

Чтобы настроить маршрутизирование транзита между вашей персональной сетью и виртуальной сетью с подключением к сайту, необходимо включить BGP на всех промежуточных соединениях от сайта к сайту, используя модель развертывания диспетчера ресурсов и PowerShell. [Узнайте, как настроить BGP на VPN-шлюзах Azure, используя для](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) инструкций PowerShell.

Транзитный трафик через VPN шлюзы Azure возможен с помощью классической модели развертывания, но он опирается на статически определенные адресные пространства в файле конфигурации сети. BGP пока не поддерживается виртуальными сетями Azure и VPN-шлюзами с помощью классической модели развертывания. Без BGP, вручную определяя транзитные адресные пространства, подвержены ошибкам, и мы не рекомендуем его.

> [!Note]
> Вы настраиваете классические соединения от сайта к сайту с помощью классического портала Azure или с помощью файла конфигурации сети в классическом портале. Вы не можете создать или изменить классическую виртуальную сеть через модель развертывания ресурсов Azure Manager или портал Azure. Для получения дополнительной информации о маршрутизации транзита для классических виртуальных сетей, [см.](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)

## <a name="configure-bgp-for-a-vpn-gateway"></a>Настройка BGP для VPN-шлюза

BGP является стандартным протоколом разгрома, используемым в Интернете для обмена информацией о разгроме и доступности между двумя или более сетями. Когда BGP используется в контексте виртуальных сетей Azure, он позволяет использовать VPN-шлюзы Azure и ваши vpn-устройства, известные как одноранговые или соседи BGP. Они обмениваются "маршрутами", которые будут информировать оба шлюза о доступности и доступности для этих префиксов, чтобы пройти через шлюзы или маршрутизаторы участие. 

BGP также может включить маршрутизацию транзита между несколькими сетями путем распространения маршрутов, которые шлюз BGP узнает от одного пэра BGP ко всем другим сверстникам BGP. Для получения дополнительной информации смотрите [Обзор BGP с Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Нанастройка BGP для VPN-соединения

Чтобы настроить VPN-соединение, использующее BGP, см. [Как настроить BGP на VPN шлюзах Azure с помощью PowerShell.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

Включите BGP на виртуальном сетевом шлюзе, создав для него номер автономной системы (AS). Основные шлюзы не поддерживают BGP. Чтобы проверить SKU шлюза, перейдите в раздел **Обзор** лезвия **VPN Gateway** на портале Azure. Если ваш SKU является **основным,** вы должны изменить SKU [(см. Изменение шлюза](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) на **VpnGw1**. 

Проверка SKU вызовет от 20 до 30 минут простоя. Как только шлюз имеет правильный SKU, вы можете добавить номер AS с помощью командного пункта [Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell. После настройки номера AS одноранговый IP-адрес BGP для шлюза будет предоставляться автоматически.

Вы должны вручную предоставить `LocalNetworkGateway` номер AS и одноранговый адрес BGP. Вы можете `ASN` установить `-BgpPeeringAddress` и значения, используя либо [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) или [Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell команд. Некоторые номера AS зарезервированы для Azure, и вы не можете использовать их, как описано в [About BGP с Azure VPN Gateway.](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)

Объект соединения должен иметь включен BGP. Вы можете `-EnableBGP` установить `$True` значение через [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) или [Set-AzureRmVirtualNetworkGatewayConnection.](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)

### <a name="validate-the-bgp-configuration"></a>Проверка конфигурации BGP

Чтобы проверить, правильно ли настроен BGP, `get-AzureRmVirtualNetworkGateway` можно `get-AzureRmLocalNetworkGateway` запустить и командовать. Затем вы заметите выход, связанный `BgpSettingsText` с BGP, в части. Пример:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Создание высокодоступного активного/активного VPN-соединения

Ключевыми различиями между активными/активными и активными/резервными шлюзами являются:

* Необходимо создать две ip-конфигурации шлюза с двумя общедоступными IP-адресами.
* Необходимо установить флаг **EnableActiveActiveFeature.**
* Шлюзs SKU должен быть **VpnGw1,** **VpnGw2**, или **VpnGw3**.

Для обеспечения высокой доступности кросс-предпосылок и подключения от сети к сети необходимо развернуть несколько VPN шлюзов и установить несколько параллельных соединений между сетями и Azure. Для обзора вариантов подключения и топологии [см.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)

Для создания активных/активных кросс-предпосылок и сетевых подключений следуйте инструкциям в [Настройке активных/активных S2S VPN-соединений с шлюзами Azure VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) для настройки vpn шлюза Azure VPN в активном/активном режиме.

> [!Note]  
> * При добавлении адресов в локальный сетевой шлюз для активного/активного режима с поддержкой BGP *добавляйте только /32 адреса узлов BGP.* Если добавить больше адресов, они будут считаться статическими маршрутами и будут иметь приоритет над маршрутами BGP.
> * Для подключений к Azure необходимо использовать различные номера BGP AS. (Если они одинаковы, вы должны изменить свой виртуальный номер сети AS, если ваше предварительное устройство VPN уже использует ASN, чтобы ввести в один ряд с другими соседями BGP.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Изменение типа шлюза VPN Azure после развертывания

Нельзя менять тип виртуального сетевого шлюза Azure с на основе политики на маршрут или наоборот напрямую. Сначала необходимо удалить шлюз. После этого IP-адрес и предварительно общий ключ не будут сохранены. Затем можно создать новый шлюз нужного типа. 

Чтобы удалить и создать шлюз, выполните следующие действия:

1. Удалите все соединения, связанные с исходным шлюзом.
2. Удалите шлюз с помощью портала Azure, PowerShell или классической PowerShell: 
   * [Удалить виртуальный сетевой шлюз с помощью портала Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Удалить виртуальный сетевой шлюз с помощью PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Удалить виртуальный сетевой шлюз с помощью PowerShell (классический)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Выполните шаги в [Создание VPN шлюза,](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) чтобы создать новый шлюз нужного типа и завершить установку VPN.

> [!Note]
> Этот процесс займет около 60 минут.

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение проблем с подключением между виртуальными машинами Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

