---
title: включение файла
description: включение файла
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814892"
---
## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить пример из этой статьи, вам нужен существующий управляемый образ универсальной виртуальной машины. Дополнительные сведения см. в статье [Руководство. Создание пользовательского образа виртуальной машины Azure с помощью Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Если управляемый образ содержит диск данных, размер диска с данными, не может быть больше 1 ТБ.

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/bash](https://shell.azure.com/bash). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

Если вы хотите установить и использовать CLI локально, см. в разделе [установить Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Создание коллекции образов 

Коллекция образов является основным ресурсом, который позволяет обмен изображениями. Допустимыми знаками для имени коллекции являются прописные или строчные буквы, цифры и точки. Имя коллекции не может содержать дефисы.   Имена коллекций должны быть уникальным в пределах вашей подписки. 

Создайте коллекцию образов, используя команду [az sig create](/cli/azure/sig#az-sig-create). В следующем примере показано, как создать коллекцию с именем *myGallery* в *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Создание определения образа

Определения образа создайте логическое группирование для изображений. Они используются для управления информацией о версии образов, создаваемых в них. Определение имен образов может состоять из прописные или строчные буквы, цифры, точки, дефисы и периодов. Дополнительные сведения о значениях, можно указать для определения образа см. в разделе [изображения определения](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

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

Допустимыми знаками для имени версии образа являются цифры и точки. Числа должны быть в диапазоне 32-битного целого числа. Формат: *MajorVersion*. *MinorVersion*. *Исправление*.

В этом примере — версия наш образ *1.0.0* и мы собираемся создать 2 реплики в *Центрально-Западная* регион, 1 репликой в *центральных штатах юга США* региона и 1 реплика в *восточная часть США 2* регион с помощью хранилища, избыточного в пределах зоны.


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
> Необходимо дождаться версию образа, чтобы полностью завершить сборки и репликацию перед один и тот же управляемый образ можно использовать для создания другой версии образа.
>
> Можно также сохранить все ваши реплики версии образа в [избыточное хранилище](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) , добавив `--storage-account-type standard_zrs` при создании версию образа.
>

## <a name="share-the-gallery"></a>Общий доступ к коллекции.

Рекомендуется, чтобы совместно использовать с другими пользователями на уровне коллекции. Чтобы получить идентификатор объекта коллекции, используйте [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Используйте идентификатор объекта в качестве области, а также адрес электронной почты и [Создание назначений ролей az](/cli/azure/role/assignment#az-role-assignment-create) чтобы предоставить пользователю доступ к общей коллекции.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


