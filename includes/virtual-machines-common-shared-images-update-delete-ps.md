---
title: включить файл
description: включить файл
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67185250"
---
## <a name="update-resources"></a>Обновление ресурсов

Есть некоторые ограничения на то, что может быть обновлено. Следующие элементы могут быть обновлены: 

Коллекция общих образов
- Описание

Определение образа
- Рекомендуемое число виртуальных ЦП
- Рекомендуемая память
- Описание
- Дата окончания жизненного цикла

Версия образа
- Количество региональных реплик
- Целевые регионы
- Исключения из последней версии
- Дата окончания жизненного цикла

Если вы планируете добавлять регионы реплик, не удаляйте управляемое исходным изображением. Для воспроизведения версии изображения в дополнительных регионах требуется исходное управляемое изображение. 

Чтобы обновить описание галереи, используйте [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

В этом примере показано, как использовать [Update-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) для обновления даты окончания срока службы для определения изображения.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

В этом примере показано, как использовать [Update-AzGalleryImageVersion,](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) чтобы исключить эту версию изображения из использования в качестве *последнего* изображения.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Очистка ресурсов

При удалении ресурсов необходимо начинать с последнего элемента в вложенных ресурсах - версии изображения. После удаления версий можно удалить определение изображения. Вы не можете удалить галерею до тех пор, пока не будут удалены все ресурсы, накоторыемываевшие под ней.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

