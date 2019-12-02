---
title: Учебник. Создание шлюза с помощью VPN-шлюза Azure и управление им
description: Руководство по созданию VPN-шлюза и управлению им с помощью модуля Azure PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: b144a70ee88138966d9cc38a56e1cff1e63fca1b
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424144"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Руководство по Создание VPN-шлюза и управление им с помощью PowerShell

VPN-шлюзы Azure предоставляют распределенное подключение между локальными сетями клиента и Azure. В этом руководстве приведены основные этапы развертывания VPN-шлюза Azure, такие как создание VPN-шлюза и управление им. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание VPN-шлюза
> * Просмотр общедоступного IP-адреса
> * Изменение размера VPN-шлюза.
> * Сброс VPN-шлюза

На следующей схеме показаны виртуальная сеть и VPN-шлюз, созданные в рамках этого руководства.

![Виртуальная сеть и VPN-шлюз](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure PowerShell в Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Значения общих параметров сети

Ниже приведены значения параметров, используемые в этом руководстве. В примерах переменные означают следующее:

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

Измените приведенные ниже значения на основе настроек среды и сети, затем скопируйте и вставьте их, чтобы указать переменные для работы с этим руководством. Если время сеанса Cloud Shell истечет или вам потребуется использовать другое окно PowerShell, скопируйте и вставьте переменные в новый сеанс, чтобы продолжить работу с этим руководством.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Сначала необходимо создать группу ресурсов. В следующем примере группа ресурсов с именем *TestRG1* создается в регионе *восточная часть США*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Создание виртуальной сети

VPN-шлюз Azure предоставляет распределенное подключение и функциональные возможности сервера VPN-подключения типа "точка — сеть" для виртуальной сети. Добавьте VPN-шлюз в имеющуюся виртуальную сеть или создайте виртуальную сеть и шлюз. Обратите внимание, что в примере явно указывается имя подсети шлюза. Всегда указывайте для подсети шлюза имя GatewaySubnet, чтобы она работала корректно. В этом примере создается виртуальная сеть с тремя подсетями (внутренняя, серверная подсеть и подсеть шлюза) с помощью команд [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) и [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Запрос общедоступного IP-адреса для VPN-шлюза

VPN-шлюзы Azure взаимодействуют с локальными VPN-устройствами через Интернет, чтобы выполнять согласование по протоколу IKE и устанавливать туннели IPsec. Создайте и назначьте общедоступный IP-адрес VPN-шлюза, как показано в приведенном ниже примере, используя команды [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) и [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> Сейчас для шлюза можно использовать только динамический общедоступный IP-адрес. Статический IP-адрес не поддерживается в VPN-шлюзах Azure.

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>Создание VPN-шлюза

Для создания VPN-шлюза требуется не менее 45 минут. Когда шлюз будет готов, можно создать подключение между вашей и другой виртуальной сетью. Также можно создать подключение между виртуальной сетью и локальным расположением. Создайте VPN-шлюз с помощью командлета [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Ниже приведены значения ключевых параметров:
* GatewayType. Используйте **Vpn** для подключений "сеть — сеть" и "виртуальная сеть — виртуальная сеть".
* VpnType. Используйте **RouteBased** для взаимодействия с более широким диапазоном VPN-устройств и получения доступа к дополнительным возможностям маршрутизации.
* GatewaySku. Значение по умолчанию — **VpnGw1**. Измените его на другой SKU VpnGw, если требуется более высокая пропускная способность или дополнительные подключения. Дополнительные сведения см. в разделе о [номерах SKU шлюзов](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Если вы используете функцию "Попробовать", время сеанса может истечь. Это не страшно. Вы по-прежнему сможете создать шлюз.

Когда шлюз будет готов, можно создать подключение между вашей и другой виртуальной сетью или между виртуальной сетью и локальным расположением. Вы также можете настроить подключение P2S к вашей виртуальной сети с клиентского компьютера.

## <a name="view-the-gateway-public-ip-address"></a>Просмотр общедоступного IP-адреса шлюза

Если вам известно имя общедоступного IP-адреса, используйте команду [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress), чтобы просмотреть общедоступный IP-адрес, назначенный шлюзу.

Если время сеанса истекло, скопируйте общие параметры сети, указанные в начале этого руководства, в новый сеанс и продолжите работу.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Изменение размера шлюза

Номер SKU VPN-шлюза можно изменить после создания шлюза. Разные номера SKU шлюза поддерживают различные спецификации (например, пропускная способность, число подключений и т. д.). В следующем примере используется команда [Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) для изменения размера шлюза с VpnGw1 на VpnGw2. Дополнительные сведения см. в разделе о [номерах SKU шлюзов](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Изменение размера VPN-шлюза также занимает около 30–45 минут. Эта операция **не** будет прерывать или удалять имеющиеся подключения и конфигурации.

## <a name="reset-a-gateway"></a>Сброс параметров шлюза

В рамках выполнения шагов по устранению неполадок вы можете сбросить VPN-шлюз Azure, чтобы выполнить принудительный перезапуск конфигураций туннеля IPsec/IKE. Используйте команду [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway), чтобы сбросить параметры шлюза.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Дополнительные сведения см. в статье [Сброс VPN-шлюза](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

При переходе к [следующему руководству](vpn-gateway-tutorial-vpnconnection-powershell.md) сохраните эти ресурсы, так как они обязательны для других действий.

Но если шлюз является частью прототипа, теста или развертывания для подтверждения концепции, вы можете использовать команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup), чтобы удалить группу ресурсов, VPN-шлюз и все связанные ресурсы.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы изучили основы создания VPN-шлюза и управления им. Вы также узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание VPN-шлюза
> * Просмотр общедоступного IP-адреса
> * Изменение размера VPN-шлюза.
> * Сброс VPN-шлюза

Перейдите к следующим руководствам, чтобы узнать о подключениях типа "сеть — сеть", "виртуальная сеть — виртуальная сеть" и "точка — сеть".

> [!div class="nextstepaction"]
> * [Создание VPN-подключений Azure S2S и управление ими с помощью модуля Azure PowerShell](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Настройка подключения VPN-шлюза между виртуальными сетями на портале Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Настройка подключения "точка — сеть" к виртуальной сети с использованием собственной аутентификации Azure на основе сертификата и портала Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
