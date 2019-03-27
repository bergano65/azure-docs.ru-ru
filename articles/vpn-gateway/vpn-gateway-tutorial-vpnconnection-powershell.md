---
title: Создание VPN-подключений Azure S2S и управление ими с помощью PowerShell | Документация Майкрософт
description: Руководство по созданию VPN-подключений Azure S2S и управлению ими с помощью модуля Azure PowerShell
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: f062dcfb22dda015d0dab9727672ca90df5ddb4d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000885"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>Руководство. Создание и администрирование VPN-подключений S2S с помощью PowerShell

VPN-подключения Azure S2S предоставляют безопасное распределенное подключение между локальными сетями клиента и Azure. Это пошаговое руководство по жизненным циклам VPN-подключений Azure S2S, таким как создание VPN-подключения Azure S2S и управление им. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание VPN-подключения S2S.
> * Обновление свойств подключения: общий ключ, BGP, политика IPsec/IKE.
> * Добавление дополнительных VPN-подключений.
> * Удаление VPN-подключения.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

На следующей схеме показана топология для этого руководства:

![Схема по VPN-подключению типа "сеть — сеть"](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="requirements"></a>Требования

Выполните инструкции из первого руководства [Создание VPN-шлюза и управление им с помощью модуля Azure PowerShell](vpn-gateway-tutorial-create-gateway-powershell.md), чтобы создать следующие ресурсы:

1. группа ресурсов (TestRG1), виртуальная сеть (VNet1) и подсеть шлюза.
2. VPN-шлюз (VNet1GW).

Ниже перечислены значения параметров виртуальной сети. Запишите дополнительные значения шлюза локальной сети, представляющие локальную сеть. Измените приведенные ниже значения на основе настроек среды и сети, затем скопируйте и вставьте их, чтобы указать переменные для работы с этим руководством. Если время сеанса Cloud Shell истечет или вам потребуется использовать другое окно PowerShell, скопируйте и вставьте переменные в новый сеанс, чтобы продолжить работу с этим руководством.

>[!NOTE]
> Если вы используете эти инструкции для создания подключения, измените значения в соответствии со своей локальной сетью. Если же вы выполняете этапы этого руководства только с целью обучения, изменения вносить не нужно, но подключение при этом работать не будет.
>

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network - LNGIP1 is the VPN device public IP address
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "5.4.3.2"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

Рабочий процесс для создания VPN-подключения Azure S2S прост:

1. Создайте шлюз локальной сети, чтобы представить локальную сеть.
2. Создайте подключение между VPN-шлюзом Azure и шлюзом локальной сети.

## <a name="create-a-local-network-gateway"></a>Создание локального сетевого шлюза

Шлюз локальной сети представляет локальную сеть. Вы можете указать свойства локальной сети в шлюзе локальной сети, включая:

* общедоступный IP-адрес вашего VPN-устройства;
* локальное адресное пространство;
* атрибуты BGP: IP-адрес партнера BGP и номер AS (необязательно).

Создайте шлюз локальной сети с помощью команды [New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway?view=azurermps-6.8.1).

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Создание VPN-подключения Azure S2S

Далее создайте VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и VPN-устройством с помощью команды [New-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?view=azurermps-6.8.1). Обратите внимание, что для VPN-подключения типа "сеть — сеть" параметр -ConnectionType имеет значение *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

Добавьте дополнительное свойство **-EnableBGP $True**, чтобы включить использование протокола BGP. Он отключен по умолчанию.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Обновление свойств VPN-подключения: общий ключ, BGP и политики IPsec/IKE

### <a name="view-and-update-your-pre-shared-key"></a>Просмотр общего ключа и его обновление

VPN-подключение Azure S2S использует общий ключ (секрет) для проверки подлинности между локальным VPN-устройством и VPN-шлюзом Azure. Вы можете просмотреть и обновить общий ключ подключения с помощью команд [Get-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey?view=azurermps-6.8.1) и [Set-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey?view=azurermps-6.8.1).

> [!IMPORTANT]
> Общий ключ — это строка **печатных символов ASCII** длиной не более 128 символов.

Эта команда показывает общий ключ для подключения:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

В результате вы получите строку с **Azure\@!b2C3** после строк, приведенных в примере выше. Используйте команду ниже, чтобы изменить значение общего ключа на **Azure\@!_b2=C3**.

```azurepowershell-interactive
Set-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Включение протокола BGP в VPN-подключении

VPN-шлюз Azure поддерживает протокол динамической маршрутизации BGP. Протокол BGP можно включить в каждом отдельном подключении, в зависимости от того, используется ли BGP в локальной сети или устройствах. Укажите следующие свойства перед включением BGP для подключения:

* ASN (номер автономной системы) VPN-подключения Azure;
* ASN шлюза локальной сети;
* IP-адреса для узла BGP шлюза локальной сети.

Если вы не настроили свойства BGP, используйте следующие команды для добавления этих свойств в VPN-шлюз и шлюз локальной сети: [Set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway?view=azurermps-6.8.1) и [Set-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway?view=azurermps-6.8.1).

Используйте пример ниже, чтобы настроить свойства BGP.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Включите BGP с помощью команды [Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection?view=azurermps-6.8.1).

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

BGP можно отключить, изменив значение свойства -EnableBGP на **$False**. Дополнительные сведения о BGP в VPN-шлюзах Azure см. в статье [Настройка BGP на VPN-шлюзах Azure с помощью PowerShell](vpn-gateway-bgp-resource-manager-ps.md).

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Применение настраиваемой политики IPsec/IKE для подключения

Вы можете применить дополнительную политику IPsec/IKE для указания точного сочетания алгоритмов шифрования IPsec/IKE и активации ключевых преимуществ в подключении, вместо того чтобы использовать [предложения по умолчанию](vpn-gateway-about-vpn-devices.md#ipsec). Следующий пример скрипта создает другую политику IPsec/IKE с такими параметрами и алгоритмами:

* IKEv2: AES256, SHA256, DHGroup14.
* IPsec: AES128, SHA1, PFS14; время существования SA — 14 400 секунд и 102 400 000 КБ.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Полный список алгоритмов и подробные инструкции см. в статье [Настройка политики IPsec/IKE для VPN-подключений типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть"](vpn-gateway-ipsecikepolicy-rm-powershell.md).

## <a name="add-another-s2s-vpn-connection"></a>Добавление другого VPN-подключения S2S

Чтобы добавить VPN-подключение S2S в тот же VPN-шлюз, создайте другой шлюз локальной сети и подключение между новым шлюзом локальной сети и VPN-шлюзом. Используйте примеры ниже, изменив значения переменных в соответствии с конфигурацией своей сети.

```azurepowershell-interactive
# On-premises network - LNGIP2 is the VPN device public IP address
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "4.3.2.1"
$Connection2 = "VNet1ToSite2"

New-AzLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Теперь у вас есть два VPN-подключения S2S к VPN-шлюзу Azure.

![Многосайтовое VPN-подключение](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Удаление VPN-подключения Azure S2S

Удалите VPN-подключение S2S с помощью команды [Remove-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection?view=azurermps-6.8.1).

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Удалите шлюз локальной сети, если он вам больше не требуется. Шлюз локальной сети нельзя удалить, если с ним связаны другие подключения.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если конфигурация является частью прототипа, теста или развертывания для подтверждения концепции, вы можете использовать команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup), чтобы удалить группу ресурсов, VPN-шлюз и все связанные ресурсы.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве описывалось создание VPN-подключений S2S и управление ими, в частности:

> [!div class="checklist"]
> * Создание VPN-подключения S2S.
> * Обновление свойств подключения: общий ключ, BGP, политика IPsec/IKE.
> * Добавление дополнительных VPN-подключений.
> * Удаление VPN-подключения.

Перейдите к следующим руководствам, чтобы узнать о подключениях типа "сеть — сеть", "виртуальная сеть — виртуальная сеть" и "точка — сеть".

> [!div class="nextstepaction"]
> * [Настройка подключения VPN-шлюза между виртуальными сетями на портале Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Настройка подключения "точка — сеть" к виртуальной сети с использованием собственной аутентификации Azure на основе сертификата и портала Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
