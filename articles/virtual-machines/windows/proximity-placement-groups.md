---
title: 'PowerShell: использование групп размещения с учетом расположения'
description: Узнайте о создании и использовании групп размещения с помощью Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208531"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Развертывание виртуальных машин в группах размещения с помощью PowerShell


Чтобы виртуальные машины максимально близки к максимально возможной задержке, следует развернуть их в [группе размещения](co-location.md#proximity-placement-groups)с учетом расположения.

Группа размещения с учетом расположения — это логическая группировка, используемая для того, чтобы ресурсы вычислений Azure физически размещались близко друг к другу. Группы размещения с учетом расположения удобны для рабочих нагрузок, где требуется низкая задержка.


## <a name="create-a-proximity-placement-group"></a>Создание группы размещения близкого взаимодействия
Создайте группу размещения с учетом расположения с помощью командлета [New-азпроксимитиплацементграуп](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) . 

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

## <a name="list-proximity-placement-groups"></a>Список групп размещения с учетом расположения

Список всех групп размещения с учетом расположения можно получить с помощью командлета [Get-азпроксимитиплацементграуп](/powershell/module/az.compute/get-azproximityplacementgroup) .

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Создание виртуальной машины

Создайте виртуальную машину в группе размещения с помощью `-ProximityPlacementGroup $ppg.Id` для ссылки на идентификатор группы размещения с учетом расположения при использовании командлета [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) для создания виртуальной машины.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Виртуальную машину можно просмотреть в группе размещения с помощью команды [Get-азпроксимитиплацементграуп](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Перемещение существующей виртуальной машины в группу размещения с учетом расположения

Можно также добавить существующую виртуальную машину в группу размещения с учетом расположения. Сначала необходимо стоп\деаллокате виртуальную машину, а затем обновить виртуальную машину и перезапустить.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Перемещение существующей виртуальной машины из группы размещения с учетом расположения

Чтобы удалить виртуальную машину из группы размещения с учетом расположения, сначала необходимо стоп\деаллокате ВИРТУАЛЬную машину, а затем обновить виртуальную машину и перезапустить.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Группы доступности
Вы также можете создать группу доступности в группе размещения с близкой назначением. Используйте один и тот же параметр `-ProximityPlacementGroup` с командлетом [New-азаваилабилитисет](/powershell/module/az.compute/new-azavailabilityset) , чтобы создать группу доступности, и все виртуальные машины, созданные в группе доступности, также будут созданы в той же группы размещения с учетом расположения.

Чтобы добавить или удалить существующую группу доступности в группе размещения с близкой назначением, необходимо сначала отключить все виртуальные машины в ней. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Перемещение существующей группы доступности в группу размещения с учетом расположения

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

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Перемещение существующего набора доступности из группы размещения с учетом расположения

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

Вы также можете создать масштабируемый набор в группе размещения с учетом расположения. Используйте один и тот же параметр `-ProximityPlacementGroup` с командлетом [New-азвмсс](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) , чтобы создать масштабируемый набор, и все экземпляры будут созданы в той же группе размещения близости.


Чтобы добавить или удалить существующий масштабируемый набор в группе размещения с учетом расположения, сначала необходимо закрыть масштабируемый набор. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Перемещение существующего масштабируемого набора в группу размещения с учетом расположения

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Перемещение существующего масштабируемого набора из группы размещения с учетом расположения

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

Можно также использовать [Azure CLI](../linux/proximity-placement-groups.md) для создания групп размещения с учетом расположения.
