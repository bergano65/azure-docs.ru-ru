---
title: включение файла
description: включение файла
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/13/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 36c4757feb367fd39ae94640cb8e8a0f1714a0d3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737260"
---
## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/bash](https://shell.azure.com/bash). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить пример из этой статьи, вам нужен существующий управляемый образ универсальной виртуальной машины. Дополнительные сведения см. в статье [Руководство. Создание пользовательского образа виртуальной машины Azure с помощью Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 

## <a name="preview-register-the-feature"></a>Предварительный просмотр: регистрация компонента

Коллекция общих образов находится в предварительной версии, но прежде чем ее можно использовать, необходимо зарегистрировать компонент. Регистрация компонента коллекции общих образов происходит следующим образом.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

Это может занять несколько минут. Ход выполнения можно проверить с помощью следующего изображения.

```azurecli-interactive
az provider show -n Microsoft.Compute
```

## <a name="create-an-image-gallery"></a>Создание коллекции образов 

Коллекция образов является основным ресурсом, который позволяет обмен изображениями. Имена коллекций должны быть уникальным в пределах вашей подписки. Создайте коллекцию образов, используя команду [az sig create](/cli/azure/sig#az-sig-create). В следующем примере показано, как создать коллекцию с именем *myGallery* в *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Создание определения образа

Создайте в коллекции начальное определения образа, используя команду [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```

## <a name="create-an-image-version"></a>Создание версии образа 
 
При необходимости создайте версии образа, используя команду [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Вам нужно ввести идентификатор управляемого образа, чтобы использовать его в качестве базового плана для создания версии образа. Для сведений об образах, содержащихся в группе ресурсов, можно использовать команду [az image list](/cli/azure/image?view#az-image-list). В этом примере — версия наш образ *1.0.0* и мы собираемся создать 5 реплик в *Центрально-Западная* регион, 1 репликой в *центральных штатах юга США* региона и 1 реплика в *восточная часть США 2* регион.

```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 5 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

