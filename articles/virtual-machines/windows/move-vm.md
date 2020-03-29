---
title: Перемещение ресурса Windows VM в Azure
description: Из этой статьи вы узнаете, как перенести виртуальную машину Windows в другую подписку или группу ресурсов Azure в модели развертывания Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: ed29c92d20a6b0d749ec44a22f42ec446ec58650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919572"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Перемещение виртуальной машины Windows в другую подписку или группу ресурсов Azure
В этой статье описано перемещение виртуальной машины Windows между группами ресурсов или подписками. Перемещение между подписками может понадобиться, если вы создали виртуальную машину в личной подписке и вам нужно переместить ее в корпоративную подписку, чтобы продолжить работу. Вам не нужно запускать VM для того, чтобы переместить его, и он должен продолжать работать во время движения.

> [!IMPORTANT]
>Во время перемещения создаются новые идентификаторы ресурсов. После перемещения виртуальной машины вам нужно будет обновить средства и сценарии, чтобы использовать новые идентификаторы ресурсов.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Использование PowerShell для перемещения виртуальной машины

Чтобы переместить виртуальную машину в другую группу ресурсов, необходимо убедиться, что также перемещаются все зависимые ресурсы. Чтобы получить список с идентификатором каждого из этих ресурсов, используйте командлет [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource).

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Выможете использовать вывод предыдущей команды для создания отдельного списка интидоув ресурса, разделенного запятой, для перемещения [ресурса](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) для перемещения каждого ресурса в пункт назначения.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Чтобы переместить ресурсы в другую подписку, добавьте параметр **-DestinationSubscriptionId**.

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


При появлении запроса на подтверждение перемещения указанных ресурсов введите **Да** для подтверждения.

## <a name="next-steps"></a>Дальнейшие действия
Вы можете перемещать разные типы ресурсов между группами ресурсов и подписками. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
