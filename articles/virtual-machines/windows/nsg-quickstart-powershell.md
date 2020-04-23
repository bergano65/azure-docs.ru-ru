---
title: Откройте порты для VM с помощью Azure PowerShell
description: Узнайте, как открыть порт или создать конечную точку для виртуальной машины Windows, используя модель развертывания с помощью Azure Resource Manager и Azure PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: a0dcc53d84edb4dd697213106c02626df24acfd8
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869396"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Как открыть порты и конечные точки для виртуальной машины в Azure с помощью PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Быстрые команды
Для создания группы безопасности сети и правил ACL потребуется [последняя версия Azure PowerShell](/powershell/azureps-cmdlets-docs). Вы также можете [выполнить эти действия с помощью портала Azure.](nsg-quickstart-portal.md)

Войдите в свою учетную запись Azure.

```powershell
Connect-AzAccount
```

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup*, *mystorageaccount* и *myVM*.

Создайте правило с помощью командлета [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). Следующий пример создает правило под названием *myNetworkSecurityGroupRule,* чтобы позволить *tcp* трафик на порту *80*:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Затем создайте группу безопасности сети с помощью командлета [New-AzureNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) и назначьте только что созданное правило HTTP, как показано ниже. Следующий пример создает группу сетевой безопасности под названием *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Теперь давайте назначим подсети вашу группу безопасности сети. В следующем примере показано назначение виртуальной сети *myVnet* переменной *$vnet* с помощью [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork).

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Свяжите группу безопасности сети с подсетью с помощью командлета [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). В следующем примере подсеть *mySubnet* связывается с группой безопасности сети:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Наконец, обновите виртуальную сеть с помощью командлета [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork), чтобы изменения вступили в силу.

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Дополнительная информация о группах безопасности сети
Приведенные здесь быстрые команды позволят настроить трафик, поступающий в виртуальную машину. Группы безопасности сети предоставляют множество полезных функций и всевозможные настройки для управления доступом к ресурсам. [Здесь](tutorial-virtual-network.md#secure-network-traffic)вы можете больше прочитать о создании группы безопасности сети и правил ACL.

Для веб-приложений с высокой доступностью необходимо поместить виртуальную машину за Azure Load Balancer. Балансировщик нагрузки распределяет трафик между виртуальными машинами с группой безопасности сети, обеспечивающей фильтрацию трафика. Подробные сведения см. в статье [Балансировка нагрузки виртуальных машин Windows в Azure для создания высокодоступного приложения](tutorial-load-balancer.md).

## <a name="next-steps"></a>Дальнейшие действия
В этом примере создано простое правило, разрешающее трафик HTTP. Информацию о создании более детализированных сред можно найти в следующих статьях.

* [Обзор менеджера ресурсов Azure](../../azure-resource-manager/management/overview.md)
* [Безопасность сети](../../virtual-network/security-overview.md)
* [Обзор баланса нагрузки Azure](../../load-balancer/load-balancer-overview.md)

