---
title: Перемещение ресурса виртуальной машины Windows в Azure | Документация Майкрософт
description: Из этой статьи вы узнаете, как перенести виртуальную машину Windows в другую подписку или группу ресурсов Azure в модели развертывания Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: d67f9e9ad99ccd6267de9dbb9fd0c64adb41f0cb
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565553"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Перемещение виртуальной машины Windows в другую подписку или группу ресурсов Azure
В этой статье описано перемещение виртуальной машины Windows между группами ресурсов или подписками. Перемещение между подписками может понадобиться, если вы создали виртуальную машину в личной подписке и вам нужно переместить ее в корпоративную подписку, чтобы продолжить работу. Необходимо запустить виртуальную Машину, чтобы переместить его и ее должны продолжать работу во время перемещения.

> [!IMPORTANT]
>Во время перемещения создаются новые идентификаторы ресурсов. После перемещения виртуальной машины вам нужно будет обновить средства и сценарии, чтобы использовать новые идентификаторы ресурсов. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Использование PowerShell для перемещения виртуальной машины

Чтобы переместить виртуальную машину в другую группу ресурсов, необходимо убедиться, что также перемещаются все зависимые ресурсы. Чтобы получить список с идентификатором каждого из этих ресурсов, используйте командлет [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource).

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId 
```

Вы можете использовать выходные данные предыдущей команды как список идентификаторов ресурсов с разделителями-запятыми в командлете [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource), чтобы переместить каждый ресурс в нужное расположение. 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
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
Вы можете перемещать разные типы ресурсов между группами ресурсов и подписками. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../../resource-group-move-resources.md).    

