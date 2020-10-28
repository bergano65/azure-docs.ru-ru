---
title: Включить файл
description: Включить файл
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: c4dcc0d578df5dbd693f4f2a05f3e531bb509838
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755477"
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

Обновите описание коллекции с помощью команды ([AZ SIG Update](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Обновите описание определения образа с помощью команды [AZ SIG Image-Definition Update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Обновите версию образа, чтобы добавить регион для репликации, с помощью команды [AZ SIG Image-Version Update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Это изменение займет некоторое время, так как образ реплицируется в новый регион.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

В этом примере показано, как использовать команду [AZ SIG Image-Version](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) , чтобы исключить эту версию изображения из использования в качестве *последнего* образа.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

В этом примере показано, как использовать команду [AZ SIG с обновлением версии](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) , чтобы включить эту версию образа в параметр, который будет рассматриваться как *последний* образ.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Удаление ресурсов

Необходимо удалить ресурсы в порядке, удалив сначала версию образа. После удаления всех версий образа вы можете удалить определение образа. Затем вы можете удалить коллекцию. 

Удалите версию образа с помощью команды [AZ SIG Image-Version Delete](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Удалите определение образа с помощью команды [AZ SIG Image-Definition Delete](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Удалите коллекцию образов с помощью команды [AZ SIG Delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
