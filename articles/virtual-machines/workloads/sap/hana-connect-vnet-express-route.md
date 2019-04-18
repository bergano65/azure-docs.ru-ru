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
ms.openlocfilehash: cb14d0784ecb87c85b02952880e9eb5744d205a2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850668"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Подключение виртуальной сети к крупным экземплярам HANA

После создания виртуальной сети Azure, вы можете подключить сеть к SAP HANA в крупных экземплярах Azure. Создайте шлюз Azure ExpressRoute в виртуальной сети. Этот шлюз позволяет привязать виртуальную сеть к каналу ExpressRoute, который подключается к клиенту пользователя в стеке крупных экземпляров.

> [!NOTE] 
> Этот шаг может занять до 30 минут, так как при этом создается шлюз в обозначенной подписке Azure, который затем подключается к указанной виртуальной сети Azure.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Если шлюз уже существует, проверьте, является ли он шлюзом ExpressRoute. Если нет, удалите шлюз и повторно создайте, как шлюз ExpressRoute. Если шлюз ExpressRoute уже установлен, обратитесь к разделу данной статьи "Связывание виртуальных сетей". 

- Используйте [портал Azure](https://portal.azure.com/) или PowerShell, чтобы создать VPN-шлюз ExpressRoute, подключенный к виртуальной сети.
  - Если вы используете портал Azure, добавьте новый **шлюз виртуальной сети**, а затем, в качестве типа шлюза выберите **ExpressRoute**.
  - Если вы используете PowerShell, сначала загрузите и используйте последнюю версию [Пакета SDK для Azure PowerShell](https://azure.microsoft.com/downloads/). Следующие команды создают шлюз ExpressRoute. Текст, перед которым стоит символ _$_ — это определяемые пользователем переменные, которые нужно обновить, используя ваши сведения.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

В этом примере использовался номер SKU шлюза HighPerformance. Существуют следующие варианты: HighPerformance или UltraPerformance. Это единственные номера SKU шлюза, которые поддерживает SAP HANA в Azure (крупные экземпляры).

> [!IMPORTANT]
> Для крупных экземпляров HANA с номерами SKU класса 2 вы должны использовать SKU "UltraPerformance Gateway".

## <a name="link-virtual-networks"></a>Связывание виртуальных сетей

Теперь, когда для виртуальной сети Azure создан шлюз ExpressRoute, используйте сведения об авторизации, предоставленные корпорацией Майкрософт, для подключения шлюза ExpressRoute к каналу ExpressRoute SAP HANA в Azure (крупные экземпляры). Вы можете подключиться с помощью портала Azure или PowerShell. Мы рекомендуем использовать портал. Но если вы хотите использовать PowerShell, далее указаны инструкции для этой службы. 

Выполните следующие команды для каждого шлюза виртуальной сети, используя разные AuthGUID для каждого подключения. Первые две записи, приведенные в сценарии ниже, получены из сведений, предоставленных корпорацией Майкрософт. Кроме того, каждый AuthGUID связан с конкретной виртуальной сетью и шлюзом. Если вы хотите добавить еще одну виртуальную сеть Azure, вам необходимо получить другой AuthID для канала ExpressRoute, по которому крупные экземпляры HANA подключаются к Azure. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Возможно, вам потребуется сделать это несколько раз, чтобы подключить шлюз к нескольким каналам ExpressRoute, связанным с вашей подпиской. Например, скорее всего, вам потребуется подключить тот же шлюз виртуальной сети к каналу ExpressRoute, с помощью которого виртуальная сеть подключается к локальной сети.

## <a name="next-steps"></a>Дальнейшие действия

- [Дополнительные требования к сети для крупных экземпляров HANA](hana-additional-network-requirements.md)
