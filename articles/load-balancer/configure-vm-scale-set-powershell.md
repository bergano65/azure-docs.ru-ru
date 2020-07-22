---
title: Настройка масштабируемого набора виртуальных машин с помощью существующего Azure Load Balancer Azure PowerShell
description: Сведения о настройке масштабируемого набора виртуальных машин с помощью существующего Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 858315008434f511e3adc0a91d591d924634fc39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809500"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Настройка масштабируемого набора виртуальных машин с использованием существующего Azure Load Balancer с помощью Azure PowerShell

В этой статье вы узнаете, как настроить в масштабируемом наборе виртуальных машин существующую Azure Load Balancer. 

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure.
- Существующий стандартный балансировщик нагрузки SKU в подписке, в которой будет развернут масштабируемый набор виртуальных машин.
- Виртуальная сеть Azure для масштабируемого набора виртуальных машин.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Вход в Azure CLI

Войдите в Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Развертывание масштабируемого набора виртуальных машин с помощью существующей подсистемы балансировки нагрузки

Замените значения в квадратных скобках именами ресурсов в конфигурации.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

В приведенном ниже примере выполняется развертывание масштабируемого набора виртуальных машин с помощью:

- Масштабируемый набор виртуальных машин с именем **myVMSS**
- Azure Load Balancer с именем **myLoadBalancer**
- Внутренний пул подсистемы балансировки нагрузки с именем **myBackendPool**
- Виртуальная сеть Azure с именем **myVnet**
- Подсеть с именем **mySubnet**
- Группа ресурсов с именем **myResourceGroup**

```azureppowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> После создания масштабируемого набора невозможно изменить внутренний порт для правила балансировки нагрузки, используемого зондом работоспособности балансировщика нагрузки. Чтобы изменить порт, можно удалить проверку работоспособности, обновив масштабируемый набор виртуальных машин Azure, обновить порт, а затем снова настроить зонд работоспособности.

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье вы развернули масштабируемый набор виртуальных машин с существующим Azure Load Balancer.  Дополнительные сведения о масштабируемых наборах виртуальных машин и подсистеме балансировки нагрузки см. в следующих статьях:

- [Что такое Azure Load Balancer](load-balancer-overview.md)
- [Что такое масштабируемый набор виртуальных машин?](../virtual-machine-scale-sets/overview.md)
                                