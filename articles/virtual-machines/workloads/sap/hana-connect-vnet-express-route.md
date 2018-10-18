---
title: Настройка подключения виртуальной сети к SAP HANA в Azure (крупные экземпляры) | Документация Майкрософт
description: Настройка подключения из виртуальной сети для использования SAP HANA в Azure (крупные экземпляры).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5efdda485e4e1f5013948c6636b267f0d388f4d5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167635"
---
# <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Подключение виртуальной сети к каналу ExpressRoute крупных экземпляров HANA

Определив диапазоны IP-адресов и получив данные из корпорации Майкрософт, вы можете приступить к подключению виртуальной сети, созданной ранее, к крупным экземплярам HANA. После создания виртуальной сети Azure в ней нужно создать шлюз ExpressRoute, чтобы связать эту виртуальную сеть с каналом ExpressRoute, подключающимся к клиенту пользователя по метке крупного экземпляра.

> [!NOTE] 
> Это может занять до 30 минут, так как при этом создается шлюз в обозначенной подписке Azure, который затем подключается к указанной виртуальной сети Azure.

Если шлюз уже существует, проверьте, является ли он шлюзом ExpressRoute. В противном случае шлюз необходимо удалить и повторно создать в качестве шлюза ExpressRoute. Если шлюз ExpressRoute уже установлен, а виртуальная сеть Azure подключена к каналу ExpressRoute для локального подключения, перейдите к разделу "Связывание виртуальных сетей" ниже.

- Используйте новый [портал Azure](https://portal.azure.com/) или PowerShell, чтобы создать VPN-шлюз ExpressRoute, подключенный к виртуальной сети.
  - Если вы используете портал Azure, добавьте новый **шлюз виртуальной сети**, а затем в качестве типа шлюза выберите **ExpressRoute**.
  - Если же вы выбрали PowerShell, сначала скачайте и используйте последнюю версию [пакета SDK для Azure PowerShell](https://azure.microsoft.com/downloads/) для оптимальной работы. Следующие команды создают шлюз ExpressRoute. Текст, перед которым стоит символ _$_, — это определяемые пользователем переменные, которые нужно обновить, используя ваши сведения.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

В этом примере использовался номер SKU шлюза HighPerformance. Имеются следующие варианты: HighPerformance или UltraPerformance. Это единственные номера SKU шлюза, которые поддерживает SAP HANA в Azure (крупные экземпляры).

> [!IMPORTANT]
> Для больших экземпляров HANA с номерами SKU класса 2 использование SKU "UltraPerformance Gateway" является обязательным.

**Связывание виртуальных сетей**

Теперь, когда для виртуальной сети Azure создан шлюз ExpressRoute, используйте сведения об авторизации, предоставленные корпорацией Майкрософт, для подключения шлюза ExpressRoute к каналу ExpressRoute SAP HANA в Azure (крупные экземпляры), созданного именно для этого. Это можно сделать на портале Azure или с помощью PowerShell. Мы рекомендуем использовать портал. Тем не менее далее указаны инструкции для PowerShell. 

- Выполните следующие команды для каждого шлюза виртуальной сети, используя разные AuthGUID для каждого подключения. Первые две записи, приведенные в сценарии ниже, получены из сведений, предоставленных корпорацией Майкрософт. Кроме того, каждый AuthGUID связан с конкретной виртуальной сетью и шлюзом. Это значит, что если вы хотите добавить еще одну виртуальную сеть Azure, вам необходимо получить другой AuthID для канала ExpressRoute, по которому крупные экземпляры HANA подключаются к Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Возможно, вам потребуется сделать это несколько раз, если вы хотите подключить шлюз к нескольким каналам ExpressRoute, связанным с вашей подпиской. Например, скорее всего, вам потребуется подключить тот же шлюз виртуальной сети к каналу ExpressRoute, с помощью которого виртуальная сеть подключается к локальной сети.

**Дальнейшие действия**

- См. статью о [дополнительных требованиях к сети для HLI](hana-additional-network-requirements.md).