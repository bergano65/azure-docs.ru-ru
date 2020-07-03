---
title: Создание образа из виртуальной машины
description: Узнайте, как создать образ в общей коллекции образов на основе виртуальной машины в Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f53a6b63c744b0e3e41f7ad22270cd842da57674
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796581"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Создание версии образа из виртуальной машины в Azure с помощью Azure CLI

Если у вас есть виртуальная машина, которую вы хотите использовать для создания нескольких идентичных виртуальных машин, можно использовать эту виртуальную машину, чтобы создать образ в общей коллекции образов с помощью Azure CLI. Вы также можете создать образ из виртуальной машины с помощью [Azure PowerShell](image-version-vm-powershell.md).

**Версия образа** — это то, что используется для создания виртуальной машины при использовании общей коллекции образов. В зависимости от требований для вашей среды, у вас может быть несколько версий образа. При использовании версии образа для создания виртуальной машины используется версия образа для создания дисков для новой виртуальной машины. Версии образов можно использовать несколько раз.


## <a name="before-you-begin"></a>Перед началом

Для работы с этой статьей необходимо иметь существующую коллекцию общих образов. 

У вас также должна быть существующая виртуальная машина в Azure в том же регионе, что и коллекция. 

Если к виртуальной машине подключен диск данных, размер диска данных не может превышать 1 ТБ.

При работе с этой статьей при необходимости замените имена ресурсов.

## <a name="get-information-about-the-vm"></a>Получение информации о виртуальной машине

Вы можете просмотреть список виртуальных машин, доступных с помощью команды [AZ VM List](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Зная имя виртуальной машины и группу ресурсов, в которой она находится, получите идентификатор виртуальной машины с помощью команды [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Создание определения образа

Определения образов создают логическую группировку для изображений. Они используются для управления сведениями о версиях образов, созданных в них. 

Имена определений образов могут состоять из букв верхнего или нижнего регистра, цифр, точек, тире и точек. 

Убедитесь, что определение образа имеет правильный тип. Если вы обобщенно используете виртуальную машину (с помощью Sysprep для Windows или waagent-unготовить для Linux), то следует создать обобщенное определение образа с `--os-state generalized`помощью. Если вы хотите использовать виртуальную машину без удаления существующих учетных записей пользователей, создайте специализированное определение `--os-state specialized`образа с помощью.

Дополнительные сведения о значениях, которые можно указать для определения изображения, см. в разделе [определения изображений](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Создайте определение образа в коллекции, выполнив команду [AZ SIG Image-Definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

В этом примере определение образа называется *мимажедефинитион*и предназначено для [специализированного](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) образа ОС Linux. Чтобы создать определение для образов с помощью ОС Windows, используйте `--os-type Windows`. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Создание версии образа

Создайте версию образа на основе виртуальной машины с помощью команды [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Допустимыми знаками для имени версии образа являются цифры и точки. Числа должны быть в диапазоне 32-битного целого числа. Формат: *majorversion*. *Minorversion*. *Исправление*.

В этом примере версия нашего образа — *1.0.0* , и мы создадим 2 реплики в регионе " *Западная Центральная часть США* ", 1 реплику в *юго-центральном регионе США* и 1 РЕПЛИКу в регионе " *Восточная часть США 2* " с использованием хранилища, избыточного в пределах зоны. Регионы репликации должны включать в себя регион, в котором находится исходная виртуальная машина.

Замените значение `--managed-image` в этом примере идентификатором виртуальной машины из предыдущего шага.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Прежде чем использовать тот же управляемый образ для создания другой версии образа, необходимо дождаться завершения сборки и репликации версии образа.
>
> Вы также можете сохранить образ в хранилище `--storage-account-type  premium_lrs`премиун, добавив хранилище или [избыточное в зону](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) , добавив `--storage-account-type  standard_zrs` его при создании версии образа.
>

## <a name="next-steps"></a>Дальнейшие действия

Создайте виртуальную машину из [обобщенного образа](vm-generalized-image-version-cli.md) , используя Azure CLI.
