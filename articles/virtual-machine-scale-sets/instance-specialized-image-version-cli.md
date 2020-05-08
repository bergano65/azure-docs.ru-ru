---
title: Создание масштабируемого набора из специализированной версии образа с помощью Azure CLI
description: Создайте масштабируемый набор с помощью специализированной версии образа в коллекции общих образов с помощью Azure CLI.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 5de9fe7c81059c56c99a55ca066e186cbf83c50f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796984"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Создание масштабируемого набора с помощью специализированной версии образа с Azure CLI

Создание масштабируемого набора из [специализированной версии образа](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) , хранящейся в коллекции общих образов. Если вы хотите создать масштабируемый набор с помощью обобщенной версии образа, см. статью [Создание виртуальной машины из обобщенного образа](instance-generalized-image-version-cli.md).

Если вы решили установить и использовать CLI локально, для работы с этим руководством вам потребуется Azure CLI версии 2.4.0 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

При необходимости замените имена ресурсов в этом примере. 

Перечислите определения образов в коллекции, выполнив команду [AZ SIG Image-Definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list) , чтобы просмотреть имя и идентификатор определений.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Создайте масштабируемый набор с [`az vmss create`](/cli/azure/vmss#az-vmss-create) помощью `--specialized` параметра, чтобы указать, что изображение является специализированным изображением.

Используйте идентификатор определения образа для `--image` , чтобы создать экземпляры масштабируемого набора из последней доступной версии образа. Можно также создать экземпляры масштабируемого набора из определенной версии, указав идентификатор версии образа для `--image`. Имейте в виду, что использование конкретной версии образа означает, что автоматизация может завершиться ошибкой, если эта версия изображения недоступна, так как она была удалена или удалена из региона. Рекомендуется использовать идентификатор определения образа для создания новой виртуальной машины, если только не требуется определенная версия образа.

В этом примере мы создаем экземпляры из последней версии образа *мимажедефинитион* .

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --Specialized
```


## <a name="next-steps"></a>Дальнейшие действия
С помощью [построителя образов Azure (Предварительная версия)](../virtual-machines/linux/image-builder-overview.md) можно автоматизировать создание версий изображений, а также использовать его для обновления и [создания новой версии образа из существующей версии образа](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

Вы также можете создать ресурс коллекции общих образов с помощью шаблонов. Существует несколько шаблонов быстрого запуска Azure: 

- [Создание Общей коллекции образов.](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Создание определения образа в Общей коллекции образов](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Создание версии образа в Общей коллекции образов](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)



