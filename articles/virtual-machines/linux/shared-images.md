---
title: Создавайте общие галереи изображений с помощью Azure CLI
description: В этой статье вы узнаете, как с помощью Azure CLI создать общий образ виртуальной машины Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: de1afa2367afcb78e8ca68e518acc93e33f61c43
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034969"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Создание коллекции общих образов с помощью Azure CLI

[Коллекция общих образов](shared-image-galleries.md) упрощает обмен пользовательскими образами в вашей организации. Пользовательские образы похожи на образы магазина, однако их можно создавать самостоятельно. Пользовательские образы можно использовать для начальной загрузки конфигураций, например при предварительной загрузке приложений, конфигураций приложений и других конфигураций операционной системы. 

Она позволяет предоставлять общий доступ к пользовательским образам виртуальной машины другим пользователям вашей организации в пределах региона или между регионами в клиенте AAD. Выберите образы, к которым нужно предоставить общий доступ, регионы, где они будут доступны, и пользователей, которым будет доступно совместное использование. Вы можете создать несколько коллекций, чтобы можно было логически группировать общие образы. 

Коллекция — это ресурс верхнего уровня, который обеспечивает полное управление доступом на основе ролей (RBAC). Образы могут быть версионными, и вы можете копировать каждую версию образа в другой набор регионов Azure. Коллекция работает только с Управляемыми образами.

Функция коллекции общих образов имеет несколько типов ресурсов. Мы будем использовать или создавать в этой статье следующие ресурсы:

| Ресурс | ОПИСАНИЕ|
|----------|------------|
| **Управляемый образ** | Это базовый образ, который можно использовать отдельно или для создания **версии образа** в коллекции образов. Управляемые образы создаются на основе обобщенных виртуальных машин. Управляемый образ — это специальный тип виртуального жесткого диска, который может применяться для создания нескольких виртуальных машин. Теперь его можно использовать для создания версий общих образов. |
| **Коллекция образов** | Как и Azure Marketplace, **коллекция образов** — это репозиторий для управления и совместного использования образов, но в отличие от Azure Marketplace доступ к коллекции образов контролируете вы. |
| **Определение образа** | Образы определяются в пределах коллекции и содержат в себе сведения об образе и требования для его использования. Эти сведения включают в себя: определение, относится ли этот образ к Windows или к Linux, заметки о выпуске, а также минимальные и максимальные требования к памяти. Это определение типа образа. |
| **Версия образа** | **Версия образа** используется для создания виртуальной машины с помощью коллекции. В зависимости от требований для вашей среды, у вас может быть несколько версий образа. Так же как и управляемый образ при использовании **версии образа** для создания виртуальной машины, версия образа используется для создания новых дисков для виртуальной машины. Версии образов можно использовать несколько раз. |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Создание виртуальной машины

Создайте виртуальную машину из последней версии образа с помощью команды [AZ VM Create](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   --resource-group myGalleryRG \
   --name myVM \
   --image "/subscriptions/subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --generate-ssh-keys
```

Вы также можете использовать определенную версию с помощью идентификатора версии образа для параметра `--image`. Например, чтобы использовать тип Image версии *1.0.0* : `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Дополнительная информация
С помощью [построителя образов Azure (Предварительная версия)](image-builder-overview.md) можно автоматизировать создание версий изображений, а также использовать его для обновления и [создания новой версии образа из существующей версии образа](image-builder-gallery-update-image-version.md). 

Вы также можете создавать ресурсы коллекции общих образов с помощью шаблонов. Существует несколько шаблонов быстрого запуска Azure: 

- [Создание коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Создание определения образа в коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Создание версии образа в коллекции общих образов](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Создание виртуальной машины из версии образа](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Дополнительные сведения о коллекциях общих образов см. в [обзорной статье](shared-image-galleries.md). Если вы столкнетесь с проблемами, обратитесь к статье об [устранении неполадок c коллекциями общих образов](troubleshooting-shared-images.md).
