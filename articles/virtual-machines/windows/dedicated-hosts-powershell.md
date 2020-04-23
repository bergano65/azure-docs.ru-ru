---
title: Развертывание выделенных узлов Azure с помощью Azure PowerShell
description: Развертывание VMs для выделенных хостов с помощью Azure PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: b90189c6ba5e51a24d0c248b5aa08e9a5e4bbd9b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082855"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Развертывание VMs для выделенных хостов с помощью Azure PowerShell

Эта статья поможет вам создать [специальный хост](dedicated-hosts.md) Azure для размещения виртуальных машин (VM). 

Убедитесь, что вы установили версию Azure PowerShell 2.8.0 или позже, `Connect-AzAccount`и вы зайдены в учетную запись Azure с помощью. 

## <a name="limitations"></a>Ограничения

- Виртуальные наборы масштабов машин в настоящее время не поддерживаются на выделенных хостах.
- Размеры и типы оборудования, доступные для выделенных узлов, различаются в зависимости от региона. Чтобы узнать больше, обратитесь к [странице ценообразования](https://aka.ms/ADHPricing) хоста.

## <a name="create-a-host-group"></a>Создание группы узлов

**Группа узлов** — это ресурс, представляющий коллекцию выделенных узлов. Вы создаете группу узлов в регионе и зоне доступности и добавляете к ней хосты. При планировании высокой доступности, Есть дополнительные варианты. Вы можете использовать один или оба из следующих вариантов с выделенными хостами: 
- Пролет через несколько зон доступности. В этом случае необходимо иметь группу хоста в каждой из зон, которые вы хотите использовать.
- Пролетчерез несколько доменов неисправностей, которые отображаются на физических стойках. 
 
В любом случае необходимо учесть количество доменов неисправностей для принимающей группы. Если вы не хотите охватывать домены неисправностей в группе, используйте количество доменов неисправностей 1. 

Вы также можете решить использовать как зоны доступности, так и домены неисправностей. Этот пример создает группу узла в зоне 1 с 2 доменами неисправности. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Создание узла

Теперь создадим специальный хост в группе-хоста. В дополнение к имени хоста, вы должны предоставить SKU для хозяина. Host SKU фиксирует поддерживаемую серию VM, а также аппаратное поколение для выделенного хоста.

Для получения дополнительной информации о хост-ску и ценах [см.](https://aka.ms/ADHPricing)

Если вы установите счет домена неисправностей для вашей хост-группы, вам будет предложено указать домен неисправности для вашего хоста. В этом примере мы устанавливаем домен неисправности для узла для 1.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Создание виртуальной машины

Создайте виртуальную машину на выделенном хосте. 

Если при создании группы хоста указана зона доступности, необходимо использовать ту же зону при создании виртуальной машины. В этом примере, поскольку наша группа принимающих компаний находится в зоне 1, нам необходимо создать VM в зоне 1.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Если вы создаете виртуальную машину на хосте, которая не имеет достаточно ресурсов, виртуальная машина будет создана в состоянии FAILED. 

## <a name="check-the-status-of-the-host"></a>Проверьте состояние хоста

Вы можете проверить состояние работоспособности хоста и сколько виртуальных машин `-InstanceView` вы все еще можете развернуть для хоста с помощью [GetAzHost](/powershell/module/az.compute/get-azhost) с параметром.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

Выходные данные будут выглядеть следующим образом.

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="add-an-existing-vm"></a>Добавление существующего VM 

Вы можете добавить существующий VM к выделенной хосте, но VM должен быть сначала stop-Deallocated. Перед тем, как переместить VM в специальный хост, убедитесь, что конфигурация VM поддерживается:

- Размер VM должен быть в семействе того же размера, что и выделенный хост. Например, если ваш выделенный хост DSv3, то размер VM может быть Standard_D4s_v3, но это не может быть Standard_A4_v2. 
- VM должен быть расположен в том же регионе, что и выделенный хост.
- VM не может быть частью группы размещения близости. Удалите VM из группы размещения близости, прежде чем перенести его в специальный хост. Для получения дополнительной информации [см. Перемещение VM из группы размещения близости](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM не может быть в наборе доступности.
- Если VM находится в зоне доступности, она должна быть такой же зоной доступности, как и группа хоста. Параметры зоны доступности для VM и принимающей группы должны соответствовать.

Замените значения переменных собственной информацией.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>Очистка

С вас взимается плата за выделенные хосты, даже если виртуальные машины не развернуты. Вы должны удалить все узлы, которые вы в настоящее время не используете, чтобы сэкономить расходы.  

Удалить хост можно только в том случае, если с его помощью больше нет виртуальных машин. Удалите vMs с помощью [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

После удаления VMs можно удалить узел с помощью [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

После удаления всех узлов можно удалить группу узлов с помощью [Узла-AzHostGroup.](/powershell/module/az.compute/remove-azhostgroup) 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Вы также можете удалить всю группу ресурсов в одной команде с помощью [Remove-AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup) Это позволит удалить все ресурсы, созданные в группе, включая все vMs, хосты и группы узлов.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Дальнейшие действия

- Существует образец шаблона, найденный [здесь,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)который использует как зоны, так и домены неисправностей для максимальной устойчивости в регионе.

- Вы также можете развернуть выделенные хосты с помощью [портала Azure.](dedicated-hosts-portal.md)
