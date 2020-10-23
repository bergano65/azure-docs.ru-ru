---
title: Создание управляемого диска из версии образа
description: Создание управляемого диска из версии образа в общей коллекции образов.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 35edcfb4bdb0715245f4a3190fb22638b1162429
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370990"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Создание управляемого диска из версии образа

При необходимости управляемый диск можно создать из версии образа, хранящейся в коллекции общих образов.


## <a name="cli"></a>CLI

Задайте `source` для переменной идентификатор версии образа, а затем выполните команду [AZ Disk Create](/cli/azure/disk#az_disk_create) , чтобы создать управляемый диск. 


Просмотреть список версий образа можно с помощью команды [AZ SIG Image-Version List](/cli/azure/sig/image-version#az_sig_image_version_list). В этом примере мы ищем все версии изображений, которые являются частью определения образа *мимажедефинитион* в коллекции образов *мигаллери* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


В этом примере мы создадим управляемый диск с именем *myManagedDisk*в регионе *EastUS* в группе ресурсов с именем *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

Если диск является диском данных, добавьте, `--gallery-image-reference-lun` чтобы указать LUN.

## <a name="powershell"></a>PowerShell

Список всех версий образа можно получить с помощью команды [Get-азресаурце](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```



После получения всех необходимых сведений можно использовать [Get-азгаллеримажеверсион](/powershell/module/az.compute/get-azgalleryimageversion) , чтобы получить версию исходного образа, которую вы хотите использовать, и назначить ее переменной. В этом примере мы получаем `1.0.0` версию образа `myImageDefinition` определения в `myGallery` коллекции исходных файлов в `myResourceGroup` группе ресурсов.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Настройте некоторые переменные для сведений о диске.

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

Создайте конфигурацию диска, а затем диск с помощью идентификатора версии исходного образа. Для `-GalleryImageReference` LUN необходим только в том случае, если источником является диск данных.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

Создайте диск.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>Дальнейшие шаги

Вы также можете создать версию образа на основе управляемого диска с помощью [Azure CLI](image-version-managed-image-cli.md) или [PowerShell](image-version-managed-image-powershell.md).


