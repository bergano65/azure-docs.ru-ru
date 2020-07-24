---
title: Создание масштабируемого набора из обобщенного образа с помощью Azure CLI
description: Создание масштабируемого набора с помощью обобщенного образа в коллекции общих образов.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3cde06b652befa8fbb655273c19f65bd2f38e850
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069841"
---
# <a name="create-a-scale-set-from-a-generalized-image-with-azure-cli"></a>Создание масштабируемого набора из обобщенного образа с помощью Azure CLI

Создайте масштабируемый набор из обобщенной версии образа, хранящейся в [общей коллекции образов](shared-image-galleries.md) , с помощью Azure CLI. Если требуется создать масштабируемый набор с помощью специализированной версии образа, см. раздел [Создание экземпляров масштабируемых наборов на основе специализированного образа](instance-specialized-image-version-cli.md).

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.4.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

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

Создайте масштабируемый набор с помощью [`az vmss create`](/cli/azure/vmss#az-vmss-create) . 

Используйте идентификатор определения образа в качестве значения параметра `--image`, чтобы создать экземпляры масштабируемого набора на основе последней доступной версии образа. Вы также можете создать экземпляры масштабируемого набора на основе определенной версии, указав идентификатор версии образа в параметре `--image`. Имейте в виду, что использование конкретной версии образа означает, что автоматизация может завершиться ошибкой, если эта версия изображения недоступна, так как она была удалена или удалена из региона. Рекомендуется использовать идентификатор определения образа для создания новой виртуальной машины, если только не требуется определенная версия образа.

В этом примере мы создаем экземпляры из последней версии образа *мимажедефинитион* .

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" 
   --admin-username azureuser \
   --generate-ssh-keys
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут.

## <a name="next-steps"></a>Дальнейшие действия
С помощью [построителя образов Azure (Предварительная версия)](../virtual-machines/linux/image-builder-overview.md) можно автоматизировать создание версий изображений, а также использовать его для обновления и [создания новой версии образа из существующей версии образа](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

Вы также можете создать ресурс коллекции общих образов с помощью шаблонов. Существует несколько шаблонов быстрого запуска Azure: 

- [Создание коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Создание определения образа в коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Создание версии образа в коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

Дополнительные сведения о коллекциях общих образов см. в [обзорной статье](shared-image-galleries.md). Если вы столкнетесь с проблемами, обратитесь к статье об [устранении неполадок c коллекциями общих образов](troubleshooting-shared-images.md).