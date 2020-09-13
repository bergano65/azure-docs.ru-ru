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
ms.openlocfilehash: 6e0612a017650f0c6e4c9f63d9a5fd097b0b92c4
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304041"
---
## <a name="update-resources"></a>Обновление ресурсов

Существуют некоторые ограничения на то, что можно обновить. Можно обновить следующие элементы: 

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

Если вы планируете добавить регионы реплик, не удаляйте исходный управляемый образ. Исходный управляемый образ необходим для репликации версии образа в дополнительные регионы. 

Чтобы обновить описание коллекции, используйте командлет [Update-азгаллери](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

В этом примере показано, как использовать [Update-азгаллеримажедефинитион](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) для обновления даты окончания срока жизни определения образа.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

В этом примере показано, как использовать командлет [Update-азгаллеримажеверсион](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) для исключения этой версии изображения из использования в качестве *последнего* образа.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

В этом примере показано, как использовать [Update-азгаллеримажеверсион](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) для включения этой версии изображения в параметр, который будет рассматриваться как *последний* образ.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

## <a name="clean-up-resources"></a>Очистка ресурсов

При удалении ресурсов необходимо начать с последнего элемента во вложенных ресурсах — версии образа. После удаления версий определение образа можно удалить. Нельзя удалить коллекцию, пока не будут удалены все ресурсы под ней.

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

