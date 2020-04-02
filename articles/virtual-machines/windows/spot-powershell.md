---
title: Используйте PowerShell для развертывания VMs Azure Spot
description: Узнайте, как использовать Azure PowerShell для развертывания спотовых выхождов для экономии затрат.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 234cf3f51173c53ef8ca15af4ca6f24881be3109
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547285"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>Развертывание Spot VMs с помощью Azure PowerShell


Использование [Spot VM](spot-vms.md) позволяет воспользоваться нашими неиспользованными мощностями при значительной экономии средств. В любой момент времени, когда Azure требуется обратной мощности, инфраструктура Azure будет выселять Spot VMs. Таким образом, Spot VMs отлично подходят для рабочих нагрузок, которые могут обрабатывать перерывы, такие как задания по обработке пакетов, среды для разработки разработчиков/тестов, большие вычислительные нагрузки и многое другое.

Цены на Spot VMs является переменной, на основе региона и SKU. Для получения дополнительной информации [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) см. [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) Для получения дополнительной информации об установлении максимальной цены, см [Spot VMs - Цены](spot-vms.md#pricing).

У вас есть возможность установить максимальную цену, которую вы готовы платить, в час, для VM. Максимальная цена на Spot VM может быть установлена в долларах США (USD), используя до 5 десятичных мест. Например, значение `0.98765`будет максимальной ценой $0.98765 USD в час. Если вы установите максимальную цену, чтобы быть, `-1`VM не будет выселен в зависимости от цены. Цена для VM будет текущая цена на спотовую или цену за стандартный VM, который всегда меньше, до тех пор, пока есть емкость и квота.


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте spotVM с помощью [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) для создания конфигурации. `-Priority Spot` Включите `-MaxPrice` и установите либо:
- `-1`так что VM не выселяются на основе цены.
- суммы в долларах, до 5 цифр. Например, `-MaxPrice .98765` означает, что VM будет иметь дело, как только цена на спот-вМ будет стоить около $.98765 в час.


Этот пример создает spotVM, который не будет рассматриваться на основе ценообразования (только тогда, когда Azure требуется обратное емкость).

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

После создания VM можно запросить максимальную цену для всех вс-баров в группе ресурсов.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Дальнейшие действия

Вы также можете создать Spot VM с помощью [Azure CLI](../linux/spot-cli.md) или [шаблона.](../linux/spot-template.md)

Если вы столкнулись с ошибкой, просмотрите [коды ошибок](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).