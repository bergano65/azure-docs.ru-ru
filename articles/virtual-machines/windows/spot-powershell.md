---
title: Развертывание виртуальных машин точки Azure с помощью PowerShell
description: Узнайте, как использовать Azure PowerShell для развертывания плашечных виртуальных машин, чтобы сэкономить на затратах.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/14/2019
ms.author: cynthn
ms.openlocfilehash: 8752522e4b5a7b91778d6eb2cd8e4ba3bac95da0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782129"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>Предварительная версия: развертывание плашечных виртуальных машин с помощью Azure PowerShell


Использование [плашечных виртуальных машин](spot-vms.md) позволяет использовать преимущества нашей неиспользуемой емкости с значительной экономией затрат. В любой момент, когда Azure нужна емкость, инфраструктура Azure будет выключать плашечные виртуальные машины. Таким образом, точечные виртуальные машины отлично подходят для рабочих нагрузок, которые могут обрабатывать прерывания, такие как задания пакетной обработки, среды разработки и тестирования, большие вычислительные рабочие нагрузки и многое другое.

Цены для плашечных виртуальных машин являются переменными в зависимости от региона и SKU. Дополнительные сведения см. в статье цены на виртуальные машины для [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) и [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Дополнительные сведения о настройке максимальной цены см. в разделе [точки на виртуальных машинах — цены](spot-vms.md#pricing).

Вы можете задать максимальную цену, которую вы хотите платить, в час, для виртуальной машины. Максимальная цена на плашечную виртуальную машину можно установить в долларах США (USD), используя до 5 десятичных разрядов. Например, значение `0.98765`будет максимальной ценой $0,98765 долл. США в час. Если задать максимальную цену `-1`, виртуальная машина не будет удалена на основе цены. Цена на виртуальную машину будет соответствовать текущей цене или цене для стандартной виртуальной машины, которая когда-либо будет меньше, если есть доступная емкость и квота.

> [!IMPORTANT]
> Экземпляры смесевых цветов в настоящее время доступны в общедоступной предварительной версии.
> Эта предварительная версия не рекомендуется для рабочих нагрузок в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> На ранних этапах общедоступной предварительной версии экземпляры смесевых цветов будут иметь фиксированную цену, поэтому на основе цен не будет никаких вытеснения.


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте Спотвм с помощью [New-азвмконфиг](/powershell/module/az.compute/new-azvmconfig) , чтобы создать конфигурацию. Включите `-Priority Spot` и задайте для `-MaxPrice` одно из следующих:
- `-1`, что виртуальная машина не будет исключена по цене.
- сумма в долларах, до 5 цифр. Например, `-MaxPrice .98765` означает, что виртуальная машина будет освобождена, когда цена за Спотвм займет около $. 98765 в час.

> [!IMPORTANT]
> На ранних этапах общедоступной предварительной версии можно задать максимальную цену, но она будет пропущена. У плашечных виртуальных машин будет фиксированная цена, поэтому вытеснения на основе цен не будет.


В этом примере создается Спотвм, который не будет освобожден на основе ценообразования (только в том случае, если Azure требуется обратная емкость).

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

После создания виртуальной машины можно выполнить запрос, чтобы увидеть максимальную цену для всех виртуальных машин в группе ресурсов.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Дальнейшие действия

Можно также создать плашечную виртуальную машину с помощью [Azure CLI](../linux/spot-cli.md) или [шаблона](../linux/spot-template.md).

Если возникает ошибка, см. раздел [коды ошибок](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).