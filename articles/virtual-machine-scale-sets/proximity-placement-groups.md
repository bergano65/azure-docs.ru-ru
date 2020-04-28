---
title: Предварительная версия групп размещения с учетом расположения для масштабируемых наборов виртуальных машин
description: Сведения о создании и использовании групп размещения с учетом расположения для масштабируемых наборов виртуальных машин Windows в Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76273621"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Предварительная версия: создание и использование групп размещения с помощью PowerShell

Чтобы виртуальные машины максимально близки к максимально возможной задержке, необходимо развернуть масштабируемый набор в [группе размещения](co-location.md#preview-proximity-placement-groups)с учетом расположения.

Группа размещения с учетом расположения — это логическая группировка, используемая для того, чтобы ресурсы вычислений Azure физически размещались близко друг к другу. Группы размещения с учетом расположения удобны для рабочих нагрузок, где требуется низкая задержка.

> [!IMPORTANT]
> Группы размещения близкого взаимодействия в настоящее время находятся в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Группы размещения с учетом расположения недоступны в этих регионах во время предварительной версии: **Восточная Япония**, **Восточная Австралия** и **Индии Central**.


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


## <a name="create-a-scale-set"></a>Создание масштабируемого набора

Создайте шкалу в группе размещения с использованием `-ProximityPlacementGroup $ppg.Id` для ссылки на идентификатор группы размещения с учетом расположения при использовании командлета [New-азвмсс](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) для создания масштабируемого набора.

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

Экземпляр можно просмотреть в группе размещения с помощью команды [Get-азпроксимитиплацементграуп](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Дальнейшие действия

Можно также использовать [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) для создания групп размещения с учетом расположения.
