---
title: Настройка виртуального набора масштабов машин с существующим балансом загрузки Azure - Azure PowerShell
description: Узнайте, как настроить виртуальный набор масштабов машин с существующим балансом загрузки Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349998"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Настроили виртуальный набор масштабов машины с существующим балансом загрузки Azure с помощью Azure PowerShell

В этой статье вы узнаете, как настроить виртуальный набор масштабов машины с существующим балансом загрузки Azure. 

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure.
- Существующий стандартный балансер загрузки sku в подписке, где будет развернут виртуальный набор масштабов машины.
- Виртуальная сеть Azure для набора виртуальной машины.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Вход в Azure CLI

Впишитесь в Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Развертывание виртуального набора масштабов машины с существующим балансером нагрузки

Замените значения в скобках именами ресурсов в конфигурации.

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

Ниже приведен пример развертывает виртуальную шкалу машины, установленную с:

- Виртуальный набор масштабов машины под названием **myVMSS**
- Балансизатор загрузки Azure назвал **myLoadBalancer**
- Нагрузка балансера бэкэнд бассейн под названием **myBackendPool**
- Виртуальная сеть Azure под названием **myVnet**
- Поднет под названием **mySubnet**
- Ресурсная группа под названием **myResourceGroup**

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
> После создания набора масштабов порт бэкэнда не может быть изменен для правила балансировки нагрузки, используемого зондом работоспособности балансоилира нагрузки. Чтобы изменить порт, можно удалить зонд работоспособности, обновив набор виртуальной машины Azure, обновить порт, а затем настроить зонд работоспособности снова.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье развернут виртуальный набор масштабов машин с существующим балансом загрузки Azure.  Чтобы узнать больше о виртуальных наборах масштабов машин и балансерере нагрузки, см.:

- [Что такое Azure Load Balancer?](load-balancer-overview.md)
- [Что такое масштабируемый набор виртуальных машин?](../virtual-machine-scale-sets/overview.md)
                                