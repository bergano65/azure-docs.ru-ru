---
title: Создание виртуальной машины из специализированной версии образа с помощью Azure CLI
description: Создайте виртуальную машину с помощью специализированной версии образа в коллекции общих образов с помощью Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1ccf03deee2a2f72c1eb2008e1acc5bf67d16447
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82796776"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Создание виртуальной машины с помощью специализированной версии образа с Azure CLI

Создайте виртуальную машину из [специализированной версии образа](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) , хранящейся в коллекции общих образов. Если вы хотите создать виртуальную машину с помощью обобщенной версии образа, см. статью [Создание виртуальной машины из обобщенной версии образа](vm-generalized-image-version-cli.md).

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

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az-vm-create), добавив к ней параметр --specialized, чтобы указать, что образ является специализированным. 

Используйте идентификатор определения образа в качестве значения параметра `--image`, чтобы создать виртуальную машину на основе последней доступной версии образа. Вы также можете создать виртуальную машину на основе определенной версии, указав идентификатор версии образа в параметре `--image`. 

В нашем примере виртуальная машина создается на основе последней версии образа *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Дальнейшие действия
С помощью [построителя образов Azure (Предварительная версия)](./linux/image-builder-overview.md) можно автоматизировать создание версий изображений, а также использовать его для обновления и [создания новой версии образа из существующей версии образа](./linux/image-builder-gallery-update-image-version.md). 

Вы также можете создать ресурс коллекции общих образов с помощью шаблонов. Существует несколько шаблонов быстрого запуска Azure: 

- [Создание коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Создание определения образа в коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Создание версии образа в коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Создание виртуальной машины из версии образа](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


