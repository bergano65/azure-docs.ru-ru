---
title: Включить имя файла
description: включить файл
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: de1a22ed6e9707767c0d097a9250f0bdd31414d5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788966"
---
## <a name="create-an-image-gallery"></a>Создание коллекции образов 

Коллекция образов является основным ресурсом, который позволяет обмен изображениями. 

Допустимыми знаками для имени коллекции являются прописные или строчные буквы, цифры и точки. Имя коллекции не может содержать дефисы.   Имена коллекций должны быть уникальным в пределах вашей подписки. 

Создайте коллекцию образов, используя команду [az sig create](/cli/azure/sig#az-sig-create). В следующем примере создается группа ресурсов с именем Gallery с именем *мигаллерирг* в *восточной части США*и коллекция с именем *мигаллери*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Предоставление общего доступа к коллекции

Вы можете совместно использовать изображения в подписках с помощью управления доступом на основе ролей (RBAC). Вы можете обмениваться образами в коллекции, определении образа или версии образа потока. Любой пользователь, имеющий разрешения на чтение для версии изображения, даже в рамках нескольких подписок, сможет развернуть виртуальную машину с помощью версии образа.

Рекомендуется предоставлять общий доступ другим пользователям на уровне коллекции. Чтобы получить идентификатор объекта коллекции, используйте команду [AZ SIG](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Используйте идентификатор объекта в качестве области, вместе с адресом электронной почты и [AZ Role назначением создать](/cli/azure/role/assignment#az-role-assignment-create) , чтобы предоставить пользователю доступ к коллекции общих образов. Замените `<email-address>` и `<gallery iD>` своими значениями.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Дополнительные сведения о предоставлении общего доступа к ресурсам с помощью RBAC см. в статье [Управление доступом с помощью RBAC и Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).
