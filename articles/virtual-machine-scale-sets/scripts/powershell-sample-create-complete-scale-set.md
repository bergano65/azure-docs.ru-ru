---
title: Примеры Azure PowerShell. Создание полного масштабируемого набора виртуальных машин | Документация Майкрософт
description: Примеры сценариев Azure PowerShell.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ebbc47739b2be72d0dd98c0659bfcaba512e79e9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448933"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Создание полного масштабируемого набора виртуальных машин с помощью PowerShell

Этот скрипт позволяет создать масштабируемый набор виртуальных машин под управлением Windows Server 2016. Индивидуальные ресурсы настраиваются и создаются чаще, чем используются [встроенные возможности создания ресурсов, доступные в New-AzVmss](powershell-sample-create-simple-scale-set.md). После выполнения сценария можно получить доступ к экземплярам виртуальных машин по протоколу RDP.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Очистка развертывания
Выполните следующую команду, чтобы удалить группу ресурсов, масштабируемый набор и все связанные с ними ресурсы.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Описание скрипта
Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Создает конфигурацию подсети. Эта конфигурация используется в процессе создания виртуальной сети. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Создает виртуальную сеть. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Создает конфигурацию внешних IP-адресов для балансировщика нагрузки. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Создает конфигурацию внутреннего пула адресов для подсистемы балансировки нагрузки. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Создает конфигурацию правил NAT для балансировщика нагрузки. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Создает подсистему балансировки нагрузки. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Создает конфигурацию пробы для балансировщика нагрузки. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Создает конфигурацию правил для балансировщика нагрузки. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Обновляет подсистему балансировки нагрузки предоставленной информацией. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | Создает конфигурацию протокола IP для экземпляров масштабируемого набора ВМ. Экземпляры ВМ подключены к подсистеме балансировки нагрузки, серверному пулу, пулу NAT и подсети виртуальной сети. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Создает конфигурацию масштабируемого набора. Такая конфигурация содержит сведения, такие как количество экземпляров создаваемых виртуальных машин, номер SKU ВМ (размер) и режим политики обновления. Конфигурация добавляется дополнительными командлетами и используется во время создания масштабируемого набора. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Определяет образ, используемый для экземпляров виртуальной машины, и добавляет его в конфигурацию масштабируемого набора. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Определяет учетные данные администратора и префикс имени виртуальной машины. Добавьте эти значения в конфигурацию масштабируемого набора. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Добавляет виртуальный сетевой интерфейс для экземпляров виртуальных машин на основе конфигурации IP. Добавьте эти значения в конфигурацию масштабируемого набора. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | Создает масштабируемый набор на основе данных, указанных в конфигурации масштабируемого набора. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

См. дополнительные примеры сценариев PowerShell для [масштабируемого набора виртуальных машин Azure](../powershell-samples.md).
