---
title: Группы размещения близости предварительный просмотр для виртуальных наборов масштабов машины
description: Узнайте о создании и использовании групп размещения непосредственной близости для наборов виртуальных машин Windows в Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273621"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Предварительный просмотр: Создание и использование групп размещения близости с помощью PowerShell

Чтобы получить ВМ как можно ближе, достигнув минимально возможной задержки, необходимо развернуть шкалу, установленную в [группе размещения.](co-location.md#preview-proximity-placement-groups)

Группа размещения близости — это логическая группировка, используемая для того, чтобы обеспечить физические расположения ресурсов Azure вычислений близко друг к другу. Группы размещения близости полезны для рабочих нагрузок, где требуется низкая задержка.

> [!IMPORTANT]
> Группа размещения близости в настоящее время находится в открытом предварительном просмотре.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Близость размещения группы не доступны в этих регионах во время предварительного просмотра: **Япония Востоке**, **Австралия Востоке** и **Индии Центральной**.


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


## <a name="create-a-scale-set"></a>Создание масштабируемого набора

Создайте шкалу в группе размещения близости, используя `-ProximityPlacementGroup $ppg.Id` для обозначения идентификатора группы размещения при использовании [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) для создания набора масштабов.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

Вы можете увидеть экземпляр в группе размещения с помощью [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Дальнейшие действия

Вы также можете использовать [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) для создания групп размещения близости.
