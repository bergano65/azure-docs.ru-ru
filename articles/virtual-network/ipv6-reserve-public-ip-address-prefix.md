---
title: Резервирование общедоступных адресов iPv6 и диапазонов адресов в виртуальной сети Azure
titlesuffix: Azure Virtual Network
description: Узнайте, как зарезервировать общедоступные адреса IPv6 и диапазоны адресов в виртуальной сети Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a0dd56842174d89688c862397c373326ef50d1f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420542"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Резервная публичная приставка адреса IPv6
IPv6 для виртуальной сети Azure (VNet) позволяет размещать приложения в Azure с подключением IPv6 и IPv4 как в виртуальной сети, так и в Интернете. В дополнение к резервированию отдельных адресов IPv6 можно зарезервировать смежные диапазоны адресов Azure IPv6 (известных как IP Prefix) для использования. В этой статье описывается, как создавать общедоступные IP-адреса и адреса IPv6 с помощью Azure PowerShell и CLI.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>Создание единого зарезервированного IP-адреса IPv6

### <a name="using-azure-powershell"></a>Использование Azure PowerShell

Вы можете создать один зарезервированный (статический) ip-адрес IPv6 с помощью Azure PowerShell с [new-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) следующим образом:

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Использование Azure CLI

 Можно создать один зарезервированный (статический) IP-адрес IPv6 Public IP-адрес Azure CLI с [помощью общедоступного IP-сообщения сети аз:](/cli/azure/network/public-ip)
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Создание зарезервированной префикса IPv6 (диапазон)

Чтобы зарезервировать префикс IPv6, добавьте семейство IP-адресов IPv6 в ту же команду, которая используется для создания префиксов IPv4. Следующие команды создают префикс размера /125 (8 адресов IPv6).  

### <a name="using-azure-powershell"></a>Использование Azure PowerShell

Можно создать общедоступный адрес IPv6 с помощью Azure CLI с [помощью общедоступного IP-сети az:](/powershell/module/az.network/new-azpublicipprefix)
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Использование Azure CLI

Создать общедоступный адрес IPv6 с помощью Azure CLI следующим образом:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Выделите общедоступный IP-адрес из зарезервированной префикса IPv6

### <a name="using-azure-powershell"></a>Использование Azure PowerShell

 Вы создаете статический IP-адрес IPv6 Public `-PublicIpPrefix` IP из зарезервированной приставки, добавляя аргумент при создании общедоступного IP с помощью Azure PowerShell. Следующий пример предполагает, что префикс был создан и сохранен в переменной PowerShell под названием: *$myOwnIPv6Prefix*.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Использование Azure CLI
 
Следующий пример предполагает, что префикс был создан и сохранен в переменной CLI под названием: *IPv6PrefixWestUS.*

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Следующие шаги
- Узнайте больше о [приставке адреса IPv6.](ipv6-public-ip-address-prefix.md)
- Узнайте больше об [адресах IPv6](ipv6-overview.md).
