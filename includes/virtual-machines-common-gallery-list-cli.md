---
title: включение файла
description: включение файла
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7fd5b2051f81a5dc34270a608c1518e8a11678b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542475"
---
## <a name="using-rbac-to-share-images"></a>Предоставление общего доступа к образам с помощью RBAC

Вы можете предоставить общий доступ к образам в разных подписках, используя механизм управления доступом на основе ролей. Любой пользователь, имеющий разрешения на чтение версии образа, даже в разных подписках, сможет с его помощью развернуть виртуальную машину.

Дополнительные сведения о предоставлении общего доступа к ресурсам с помощью RBAC см. в статье [Управление доступом с помощью RBAC и Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Просмотр сведений

Чтобы просмотреть сведения о расположении, состоянии и другие данные о доступных коллекциях образов, выполните команду [az sig list](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Чтобы просмотреть определения образов в коллекции, в том числе сведения о состоянии и типе ОС, выполните команду [az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

Чтобы просмотреть версии общих образов в коллекции, выполните команду [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myGalleryImage -o table
```

Чтобы получить идентификатор версии образа, выполните команду [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```
az sig image-version show \
-g myGalleryRG \     
-r myGallery \     
-i myGalleryImage \     
--gallery-image-version-name 1.0.0 \     
--query "id"
```