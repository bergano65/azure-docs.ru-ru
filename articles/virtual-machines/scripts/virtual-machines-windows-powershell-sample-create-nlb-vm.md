---
title: Пример сценария Azure PowerShell для создания виртуальной машины Windows с балансировкой нагрузки.
description: Пример сценария Azure PowerShell для создания виртуальной машины Windows с балансировкой нагрузки.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 2501a2476c1a26b09cbc33bb4e12463eb2c734d3
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078819"
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>Балансировка нагрузки для трафика между высокодоступными виртуальными машинами

Этот пример скрипта позволяет создать все необходимые компоненты для запуска нескольких виртуальных машин Windows Server 2016, настроенных в высокодоступной конфигурации с балансировкой нагрузки. После выполнения этого сценария будут созданы три виртуальные машины, которые будут добавлены в группу доступности Azure. Доступ к ним можно будет получить через Azure Load Balancer.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Create VM NLB")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Создает конфигурацию подсети. Эта конфигурация используется в процессе создания виртуальной сети. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Создает виртуальную сеть. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Создает конфигурацию внешних IP-адресов для балансировщика нагрузки. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Создает конфигурацию внутреннего пула адресов для подсистемы балансировки нагрузки. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Создает конфигурацию пробы для балансировщика нагрузки. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Создает конфигурацию правил для балансировщика нагрузки. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Создает конфигурацию правил NAT для балансировщика нагрузки. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Создает подсистему балансировки нагрузки. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Создает конфигурацию правил группы безопасности сети. Эта конфигурация используется для создания правила NSG при создании этой NSG. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Создает группу безопасности сети. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Возвращает сведения о подсети. Эта информация используется при создании сетевого интерфейса. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Создает сетевой интерфейс. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Создает конфигурацию виртуальной машины. Эта конфигурация включает в себя такие сведения, как имя виртуальной машины, операционную систему и учетные данные администратора. Данная конфигурации используется при создании виртуальной машины. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Создайте виртуальную машину. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

Виртуальные машины можно также создать с помощью собственного пользовательского управляемого образа. В конфигурации виртуальной машины для `Set-AzVMSourceImage` используйте параметры `-Id` и `-VM` вместо `-PublisherName`, `-Offer`, `-Skus`, и `-Version`.

Например, создание конфигурации виртуальной машины будет выглядеть следующим образом:

```powershell
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 -AvailabilitySetId $as.Id | `
  Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred | `
  Set-AzVMSourceImage -Id <Image.ID of the custom managed image> | Add-AzVMNetworkInterface -Id $nicVM3.Id
 ```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
