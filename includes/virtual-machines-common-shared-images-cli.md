---
title: включить файл
description: включить файл
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "66814892"
---
## <a name="before-you-begin"></a>Перед началом

Чтобы выполнить пример из этой статьи, вам нужен существующий управляемый образ универсальной виртуальной машины. Дополнительные сведения см. [в разделе Учебник. Создание пользовательского образа виртуальной машины Azure с помощью Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Если управляемый образ содержит диск данных, размер диска данных не может превышать 1 ТБ.

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/bash](https://shell.azure.com/bash). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

Если вы предпочитаете установить и использовать интерфейс командной строки локально, см. раздел [install Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Создание коллекции образов 

Коллекция образов является основным ресурсом, который позволяет обмен изображениями. Допустимыми знаками для имени коллекции являются прописные или строчные буквы, цифры и точки. Имя коллекции не может содержать дефисы.   Имена коллекций должны быть уникальным в пределах вашей подписки. 

Создайте коллекцию образов, используя команду [az sig create](/cli/azure/sig#az-sig-create). В следующем примере показано, как создать коллекцию с именем *myGallery* в *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Создание определения образа

Определения образов создают логическую группировку для изображений. Они используются для управления сведениями о версиях образов, созданных в них. Имена определений образов могут состоять из букв верхнего или нижнего регистра, цифр, точек, тире и точек. Дополнительные сведения о значениях, которые можно указать для определения изображения, см. в разделе [определения изображений](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Создайте в коллекции начальное определения образа, используя команду [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>Создание версии образа 

При необходимости создайте версии образа, используя команду [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Вам нужно ввести идентификатор управляемого образа, чтобы использовать его в качестве базового плана для создания версии образа. Для сведений об образах, содержащихся в группе ресурсов, можно использовать команду [az image list](/cli/azure/image?view#az-image-list). 

Допустимыми знаками для имени версии образа являются цифры и точки. Числа должны быть в диапазоне 32-битного целого числа. Формат: *majorversion*. *Minorversion*. *Исправление*.

В этом примере версия нашего образа — *1.0.0* , и мы создадим 2 реплики в регионе " *Западная Центральная часть США* ", 1 реплику в *юго-центральном регионе США* и 1 РЕПЛИКу в регионе " *Восточная часть США 2* " с использованием хранилища, избыточного в пределах зоны.


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> Прежде чем использовать тот же управляемый образ для создания другой версии образа, необходимо дождаться завершения сборки и репликации версии образа.
>
> Вы также можете хранить все реплики версии образа в хранилище, [избыточном](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) в виде зоны `--storage-account-type standard_zrs` , путем добавления при создании версии образа.
>

## <a name="share-the-gallery"></a>Предоставление общего доступа к коллекции

Рекомендуется предоставлять общий доступ другим пользователям на уровне коллекции. Чтобы получить идентификатор объекта коллекции, используйте команду [AZ SIG](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Используйте идентификатор объекта в качестве области, вместе с адресом электронной почты и [AZ Role назначением создать](/cli/azure/role/assignment#az-role-assignment-create) , чтобы предоставить пользователю доступ к коллекции общих образов.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


