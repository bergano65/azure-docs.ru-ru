---
title: 'PowerShell: Использование групп размещения близости'
description: Узнайте о создании и использовании групп размещения непосредственнок с помощью Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208531"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Развертывание ВМ в группы размещения непосредственнок с помощью PowerShell


Чтобы получить ВМ как можно ближе, достигнув минимально возможной задержки, вы должны развернуть их в [группе размещения близости.](co-location.md#proximity-placement-groups)

Группа размещения близости — это логическая группировка, используемая для того, чтобы обеспечить физические расположения ресурсов Azure вычислений близко друг к другу. Группы размещения близости полезны для рабочих нагрузок, где требуется низкая задержка.


## <a name="create-a-proximity-placement-group"></a>Создание группы размещения близкого взаимодействия
Создайте группу размещения близости с помощью [cmdlet New-AzProximityPlacementGroup.](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Список групп размещения близости

Вы можете перечислить все группы размещения близости с помощью [cmdlet Get-AzProximityPlacementGroup.](/powershell/module/az.compute/get-azproximityplacementgroup)

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Создание виртуальной машины

Создайте VM в группе `-ProximityPlacementGroup $ppg.Id` размещения близости, используя для обозначения идентификатора группы размещения при использовании [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) для создания VM.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Вы можете увидеть VM в группе размещения с помощью [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Переместите существующий VM в группу размещения

Вы также можете добавить существующий VM в группу размещения близости. Сначала необходимо остановить выделение VM, а затем обновить VM и перезапустить его.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Перемещение существующего VM из группы размещения близости

Чтобы удалить VM из группы размещения близости, необходимо сначала остановить выделение VM, а затем обновить VM и перезапустить.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Группы доступности
Вы также можете создать набор доступности в вашей группе размещения близости. Используйте `-ProximityPlacementGroup` тот же параметр с cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) для создания набора доступности, и все ввосполните VMs в наборе доступности, также будут созданы в той же группе размещения близости.

Чтобы добавить или удалить существующий набор доступности в группу размещения непоместителей, сначала необходимо остановить все вс-ми в наборе доступности. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Перемещение существующего набора доступности в группу размещения непосредственного доступа

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Перемещение существующей доступности, установленной из группы размещения непосредственного доступа

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Масштабируемые наборы

Вы также можете создать набор шкалы в группе размещения близости. Используйте `-ProximityPlacementGroup` тот же параметр с [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) для создания набора масштабов, и все экземпляры будут созданы в одной и той же группе размещения.


Чтобы добавить или удалить существующий набор шкалы в группу размещения непосредственной близости, сначала необходимо остановить набор масштабов. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Перемещение существующей шкалы, установленной в группу размещения непосредственной близости

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Перемещение существующей шкалы, установленной из группы размещения непосредственного размещения

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

Вы также можете использовать [Azure CLI](../linux/proximity-placement-groups.md) для создания групп размещения близости.
