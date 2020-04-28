---
title: включить файл
description: включить файл
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ec3ecdafb8e475f5f13372789528612ccd7b8b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "66226023"
---
## <a name="using-rbac-to-share-images"></a>Предоставление общего доступа к образам с помощью RBAC

Вы можете совместно использовать изображения в подписках с помощью управления доступом на основе ролей (RBAC). Любой пользователь, имеющий разрешения на чтение версии образа даже в разных подписках, сможет развернуть виртуальную машину с помощью версии образа.

Дополнительные сведения о предоставлении общего доступа к ресурсам с помощью RBAC см. в статье [Управление доступом с помощью RBAC и Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Просмотр сведений

Чтобы просмотреть сведения о расположении, состоянии и другие данные о доступных коллекциях образов, выполните команду [az sig list](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Чтобы просмотреть определения образов в коллекции, в том числе сведения о состоянии и типе ОС, выполните команду [az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Чтобы просмотреть версии общих образов в коллекции, выполните команду [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Чтобы получить идентификатор версии образа, выполните команду [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```