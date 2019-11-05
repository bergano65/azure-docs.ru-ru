---
title: Настраиваемые селекторы трафика для подключений VPN-шлюза Azure | Документация Майкрософт
description: Дополнительные сведения об использовании настраиваемых селекторов трафика в подключениях VPN-шлюза
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: radwiv
ms.openlocfilehash: cf9401b21d4aa736a6edeae5146d1355f2d49d1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512162"
---
# <a name="custom-traffic-selectors-for-vpn-gateway-connections"></a>Настраиваемые селекторы трафика для подключений VPN-шлюза

Вы можете задать настраиваемые селекторы трафика в подключениях VPN-шлюза. Возможность установки селекторов трафика позволяет ограничивать адресные пространства от обеих сторон VPN-туннелей, для которых требуется отправить трафик. Настраиваемые селекторы трафика особенно полезны при наличии большого адресного пространства виртуальной сети, но необходимо использовать одну из подсетей для согласования IPsec/IKE.

Настраиваемые селекторы трафика могут быть добавлены при создании нового соединения или могут быть обновлены для существующего соединения. Параметр `TrafficSelectorPolicies` состоит из массива селекторов трафика. Каждый селектор трафика содержит коллекцию локальных и удаленных диапазонов адресов в формате CIDR.

## <a name="add-custom-traffic-selectors"></a>Добавление настраиваемых селекторов трафика

В следующих примерах показано, как можно создать настраиваемые селекторы трафика для подключения шлюза виртуальной сети с помощью команд PowerShell. Дополнительные сведения о создании подключения шлюза виртуальной сети см. в статье [Настройка подключения типа "сеть — сеть](vpn-gateway-create-site-to-site-rm-powershell.md)".

1. Задайте значения для параметра *траффикселекторполиЦиес* в $trafficselectorpolicy. При задании этого значения параметр *New-азтраффикселекторполици* принимает локальные и удаленные диапазоны адресов в массиве.

   ```azurepowershell-interactive
   $trafficSelectorPolicy = New-AzTrafficSelectorPolicy `
   -LocalAddressRanges ("10.10.10.0/24", "20.20.20.0/24") `
   -RemoteAddressRanges ("30.30.30.0/24", "40.40.40.0/24")
   ```
2. Создайте новое подключение к шлюзу виртуальной сети. Измените значения параметров для требований.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection `
   -ResourceGroupName $rgname `
   -name $vnetConnectionName `
   -location $location `
   -VirtualNetworkGateway1 $vnetGateway `
   -LocalNetworkGateway2 $localnetGateway `
   -ConnectionType IPsec `
   -RoutingWeight 3 `
   -SharedKey $sharedKey `
   -UsePolicyBasedTrafficSelectors $true `
   -TrafficSelectorPolicies ($trafficSelectorPolicy)
   ```

Вы также можете обновить настраиваемые селекторы трафика для существующего подключения к шлюзу виртуальной сети с помощью команды "Set-Азвиртуалнетворкгатевайконнектион". Значения для PowerShell см. в статье [Подключение к шлюзу виртуальной сети](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о VPN-шлюзах см. в статье [о VPN-шлюзе](vpn-gateway-about-vpngateways.md).