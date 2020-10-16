---
title: включить файл
description: включить файл
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b49dc6ef2bfee311bc3ca524a5ccb0a4e4b5ca9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793675"
---
## <a name="create-an-image-gallery"></a>Создание коллекции образов 

Коллекция образов является основным ресурсом, который позволяет обмен изображениями. 

Допустимыми знаками для имени коллекции являются прописные или строчные буквы, цифры и точки. Имя коллекции не должно содержать дефисы.   Имена коллекций должны быть уникальным в пределах вашей подписки. 

Создайте коллекцию образов, используя команду [az sig create](/cli/azure/sig#az-sig-create). В следующем примере показано, как создать группу ресурсов с именем *myGalleryRG* в регионе *восточная часть США* и коллекцию с именем *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Предоставление общего доступа к коллекции

Вы можете предоставить общий доступ к образам в разных подписках, используя механизм управления доступом на основе ролей (RBAC). Вы можете обмениваться изображениями в коллекции, на уровне определения образа или изображения. Любой пользователь с разрешениями на чтение версии образа, даже из другой подписки, сможет развернуть виртуальную машину с помощью версии образа.

Мы рекомендуем предоставлять общий доступ другим пользователям на уровне коллекции. Чтобы получить идентификатор объекта коллекции, используйте команду [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Используйте идентификатор объекта в качестве области, а также адрес электронной почты и команду [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create), чтобы предоставить пользователю доступ к общей коллекции образов. Замените `<email-address>` и `<gallery iD>` своими значениями.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Дополнительные сведения о предоставлении общего доступа к ресурсам с помощью RBAC см. в статье [Управление доступом с помощью RBAC и Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).
