---
title: Использование групп размещения близости для виртуальных машин Windows
description: Узнайте, как создавать и использовать группы размещения близкого взаимодействия для виртуальных машин Windows в Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d0c35737151b060dcffba8944f4a1361d36dc14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171220"
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


## <a name="create-a-vm"></a>Создание ВМ

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

## <a name="availability-sets"></a>Группы доступности
Вы также можете создать группу доступности в группе размещения с близкой назначением. Используйте один и тот же параметр `-ProximityPlacementGroup` с командлетом [New-азаваилабилитисет](/powershell/module/az.compute/new-azavailabilityset) , чтобы создать группу доступности, и все виртуальные машины, созданные в группе доступности, также будут созданы в той же группы размещения с учетом расположения.

## <a name="scale-sets"></a>Масштабируемые наборы

Вы также можете создать масштабируемый набор в группе размещения с учетом расположения. Используйте один и тот же параметр `-ProximityPlacementGroup` с командлетом [New-азвмсс](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) , чтобы создать масштабируемый набор, и все экземпляры будут созданы в той же группе размещения близости.

## <a name="next-steps"></a>Дальнейшие действия

Можно также использовать [Azure CLI](../linux/proximity-placement-groups.md) для создания групп размещения с учетом расположения.
