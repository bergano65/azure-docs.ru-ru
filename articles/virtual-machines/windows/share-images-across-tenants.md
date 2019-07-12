---
title: Совместное использование образов из коллекции клиентов в Azure | Документация Майкрософт
description: Узнайте, как совместно использовать образы виртуальных Машин в Azure клиентов, использующих общий галерей изображений.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: c26abe948fa415c780d543c615c34af2091cfbc7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709161"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Совместное использование коллекции образов виртуальных Машин в Azure клиентов

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> Чтобы развернуть виртуальную Машину из образа в другом клиенте azure нельзя использовать портал. Чтобы создать виртуальную Машину из образа совместно несколькими клиентами, необходимо использовать [Azure CLI](../linux/share-images-across-tenants.md) или Powershell.

## <a name="create-a-vm-using-powershell"></a>Создание виртуальной Машины с помощью PowerShell


Войдите в обоих клиентов, используя идентификатор приложения, секрет и идентификатор клиента. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

Создайте виртуальную Машину в группе ресурсов, которая имеет разрешение на регистрацию приложения. Замените сведения в этом примере на собственные.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $image `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

## <a name="next-steps"></a>Следующие шаги

Можно также создать общего образа коллекции ресурсов с помощью [портала Azure](shared-images-portal.md).