---
title: 'VPN-шлюз Azure: Создание шлюза на основе маршрута: PowerShell'
description: Быстрое создание VPN-шлюза на основе маршрутов с помощью PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/11/2019
ms.author: cherylmc
ms.openlocfilehash: 4e5a3fa8068b55f67246832cbc706fc4a9417cb3
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151590"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Создание VPN-шлюза на основе маршрутов с помощью PowerShell

Эта статья поможет быстро создать VPN-шлюз Azure на основе маршрутов, используя PowerShell. VPN-шлюз используется при создании VPN-подключения к локальной сети. Также вы можете использовать VPN-шлюз для подключения виртуальных сетей.

Инструкции в этой статье позволяют создать виртуальную сеть, подсеть, подсеть шлюза и VPN-шлюз на основе маршрутов (шлюз виртуальной сети). Создав шлюз, можно создавать подключения. Для этих действий требуется наличие подписки Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="vnet"></a>Создание виртуальной сети

Создайте виртуальную сеть с помощью командлета [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). В следующем примере создается виртуальная сеть с именем **VNet1** в расположении **EastUS**.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Создайте конфигурацию подсети с помощью командлета [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig).

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Задайте конфигурацию подсети для виртуальной сети с помощью командлета [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork).


```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="gwsubnet"></a>Добавление подсети шлюза

Подсеть шлюза содержит зарезервированные IP-адреса, которые используются службами шлюза виртуальной сети. Используйте следующие примеры для добавления подсети шлюза:

Задайте переменную для виртуальной сети.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Создайте подсеть шлюза с помощью командлета [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-azVirtualNetworkSubnetConfig).

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Задайте конфигурацию подсети для виртуальной сети с помощью командлета [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork).

```azurepowershell-interactive
$vnet | Set-AzVirtualNetwork
```

## <a name="PublicIP"></a>Запрос общедоступного IP-адреса

VPN-шлюз должен иметь динамически выделяемый общедоступный IP-адрес. При создании подключения к шлюзу VPN это — IP-адрес, который вы указываете. Воспользуйтесь следующим примером для запроса общедоступного IP-адреса:

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>Создание конфигурации IP-адреса шлюза

Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="CreateGateway"></a>Создание VPN-шлюза

Для создания VPN-шлюза требуется не менее 45 минут. Когда шлюз будет готов, можно создать подключение между вашей и другой виртуальной сетью. Также можно создать подключение между виртуальной сетью и локальным расположением. Создайте VPN-шлюз с помощью командлета [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="viewgw"></a>Просмотр VPN-шлюза

Просмотреть VPN-шлюз можно с помощью командлета [Get-AzVirtualNetworkGateway](/powershell/module/az.network/Get-azVirtualNetworkGateway).

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

Выходные данные будут выглядеть приблизительно так:

```
Name                   : VNet1GW
ResourceGroupName      : TestRG1
Location               : eastus
Id                     : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provide
                         rs/Microsoft.Network/virtualNetworkGateways/VNet1GW
Etag                   : W/"0952d-9da8-4d7d-a8ed-28c8ca0413"
ResourceGuid           : dc6ce1de-2c4494-9d0b-20b03ac595
ProvisioningState      : Succeeded
Tags                   :
IpConfigurations       : [
                           {
                             "PrivateIpAllocationMethod": "Dynamic",
                             "Subnet": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet"
                             },
                             "PublicIpAddress": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP"
                             },
                             "Name": "default",
                             "Etag": "W/\"0952d-9da8-4d7d-a8ed-28c8ca0413\"",
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                         RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/de
                         fault"
                           }
                         ]
GatewayType            : Vpn
VpnType                : RouteBased
EnableBgp              : False
ActiveActive           : False
GatewayDefaultSite     : null
Sku                    : {
                           "Capacity": 2,
                           "Name": "VpnGw1",
                           "Tier": "VpnGw1"
                         }
VpnClientConfiguration : null
BgpSettings            : {
     
```

## <a name="viewgwpip"></a>Просмотр общедоступного IP-адреса

Чтобы просмотреть общедоступный IP-адрес VPN-шлюза, используйте командлет [Get-AzPublicIpAddress](/powershell/module/az.network/Get-azPublicIpAddress).

```azurepowershell-interactive
Get-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

В примере ответа значение IpAddress будет общедоступным IP-адресом.

```
Name                     : VNet1GWIP
ResourceGroupName        : TestRG1
Location                 : eastus
Id                       : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provi
                           ders/Microsoft.Network/publicIPAddresses/VNet1GWIP
Etag                     : W/"5001666a-bc2a-484b-bcf5-ad488dabd8ca"
ResourceGuid             : 3c7c481e-9828-4dae-abdc-f95b383
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.90.153.3
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                           RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/
                           default"
                           }
DnsSettings              : null
Zones                    : {}
Sku                      : {
                             "Name": "Basic"
                           }
IpTags                   : {}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда созданные ресурсы больше не будут нужны, удалите группу ресурсов с помощью команды [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). При этом будет удалена группа ресурсов и все содержащиеся в ней ресурсы.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Дополнительная информация

Когда завершится создание шлюза, вы можете создать подключение между этой виртуальной сетью и другой виртуальной сетью. Также можно создать подключение между виртуальной сетью и локальным расположением.

> [!div class="nextstepaction"]
> [Создание подключения "сеть — сеть"](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Создание подключения "точка — сеть"](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Создание подключения к другой виртуальной сети](vpn-gateway-vnet-vnet-rm-ps.md)
