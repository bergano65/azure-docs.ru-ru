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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814892"
---
## <a name="before-you-begin"></a>Перед началом

Чтобы выполнить пример из этой статьи, вам нужен существующий управляемый образ универсальной виртуальной машины. Для получения дополнительной информации [см. Tutorial: Создайте пользовательское изображение VM Azure с помощью Azure CLI.](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images) Если управляемое изображение содержит диск данных, размер диска данных не может быть более 1 ТБ.

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Вы также можете запустить Cloud Shell в [https://shell.azure.com/bash](https://shell.azure.com/bash)отдельной вкладке браузера, перейдя к . Выберите **Copy,** чтобы скопировать блоки кода, вставьте его в облачную оболочку и нажмите введите, чтобы запустить его.

Если вы предпочитаете устанавливать и использовать CLI локально, [см.](/cli/azure/install-azure-cli)

## <a name="create-an-image-gallery"></a>Создание коллекции образов 

Коллекция образов является основным ресурсом, который позволяет обмен изображениями. Допустимыми знаками для имени коллекции являются прописные или строчные буквы, цифры и точки. Название галереи не может содержать тире.   Имена коллекций должны быть уникальным в пределах вашей подписки. 

Создайте коллекцию образов, используя команду [az sig create](/cli/azure/sig#az-sig-create). В следующем примере показано, как создать коллекцию с именем *myGallery* в *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Создание определения образа

Определения изображений создают логическую группировку изображений. Они используются для управления информацией о версиях изображений, которые создаются в них. Имена определения изображения могут состоять из верхних или нижних букв, цифр, точек, тире и периодов. Для получения дополнительной информации о значениях, которые вы можете указать для определения изображения, [см.](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)

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

Допустимыми знаками для имени версии образа являются цифры и точки. Числа должны быть в диапазоне 32-битного целого числа. Формат: *MajorVersion*. *MinorVersion*. *Патч*.

В этом примере версия нашего изображения *составляет 1.0.0,* и мы собираемся создать 2 реплики в *регионе Западного Центрального США,* 1 реплику в *южном центральном* регионе США и 1 реплику в восточном регионе *США 2* с использованием зоны избыточного хранения.


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
> Вам нужно ждать, пока версия изображения полностью закончит строиться и реплицироваться, прежде чем вы сможете использовать то же управляемое изображение для создания другой версии изображения.
>
> Вы также можете хранить все реплики версии изображений `--storage-account-type standard_zrs` в [зоне избыточного хранения,](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) добавляя при создании версии изображения.
>

## <a name="share-the-gallery"></a>Поделитесь галереей

Мы рекомендуем вам поделиться с другими пользователями на уровне галереи. Чтобы получить идентификатор объекта вашей галереи, используйте [шоу az sig.](/cli/azure/sig#az-sig-show)

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Используйте идентификатор объекта в качестве области, а также адрес электронной почты и [создание ролевого назначения аз,](/cli/azure/role/assignment#az-role-assignment-create) чтобы дать пользователю доступ к общей галерее изображений.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


