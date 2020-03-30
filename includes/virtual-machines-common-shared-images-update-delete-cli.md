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
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67185299"
---
## <a name="update-resources"></a>Обновление ресурсов

Есть некоторые ограничения на то, что может быть обновлено. Следующие элементы могут быть обновлены: 

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

Если вы планируете добавлять регионы реплик, не удаляйте управляемое исходным изображением. Для воспроизведения версии изображения в дополнительных регионах требуется исходное управляемое изображение. 

Обновление описания галереи с помощью ([az SIG обновление](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Обновите описание определения изображения с помощью [обновления определения изображения az sig.](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Обновление версии изображения, чтобы добавить область, чтобы воспроизвести с помощью [az sig обновления изображения-версии.](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) Это изменение займет некоторое время, как изображение получает реплицируется в новый регион.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Удаление ресурсов

Вы должны удалить ресурсы в обратном порядке, удалив версию изображения в первую очередь. После удаления всех версий образа вы можете удалить определение образа. Затем вы можете удалить коллекцию. 

Удалите версию изображения с помощью [az sig-версии изображения.](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete)

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Удалите определение изображения с помощью [удаления изображения az sig.](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete)

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Удалите галерею изображений с помощью [удаления az sig.](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete)

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
