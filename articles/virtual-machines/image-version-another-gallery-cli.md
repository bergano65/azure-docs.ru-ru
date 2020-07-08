---
title: Копировать версию образа из другой коллекции
description: Скопируйте версию образа из другой коллекции с Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: add08d7b8ef39322f03e0faf78959b08a6ae2a14
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82797062"
---
# <a name="copy-an-image-from-another-gallery"></a>Копирование изображения из другой коллекции

При наличии нескольких коллекций в Организации можно также создавать версии образов из существующих версий образов, хранящихся в других галереях. Например, у вас может быть коллекция разработки и тестирования для создания и тестирования новых образов. Когда они готовы к использованию в рабочей среде, их можно скопировать в рабочую коллекцию, используя этот пример. Вы также можете создать образ из изображения в другой коллекции, используя [Azure PowerShell](image-version-another-gallery-powershell.md).



## <a name="before-you-begin"></a>Перед началом

Для работы с этой статьей необходимо наличие существующей коллекции источников, определения образа и версии образа. Кроме того, у вас должна быть конечная коллекция. 

Исходная версия образа должна быть реплицирована в регион, где находится конечная коллекция. 

При работе с этой статьей при необходимости замените имена ресурсов.



## <a name="get-information-from-the-source-gallery"></a>Получение сведений из коллекции источников

Вам понадобятся сведения из определения исходного образа, чтобы можно было создать его копию в новой коллекции.

Выведите список сведений о доступных галереях изображений с помощью команды [AZ SIG List](/cli/azure/sig#az-sig-list) , чтобы найти сведения о коллекции источников.

```azurecli-interactive 
az sig list -o table
```

Перечислите определения образов в галерее с помощью команды [AZ SIG Image-Definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list). В этом примере выполняется поиск определений изображений в коллекции с именем *мигаллери* в группе ресурсов *мигаллерирг* .

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Выведите список версий образа в коллекции, выполнив команду [AZ SIG Image-Version List](/cli/azure/sig/image-version#az-sig-image-version-list) , чтобы найти версию образа, которую необходимо скопировать в новую галерею. В этом примере мы ищем все версии изображений, которые являются частью определения образа *мимажедефинитион* .

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

После получения всей необходимой информации можно получить идентификатор исходной версии образа, выполнив команду [AZ SIG Image-Version демонстрация](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Создание определения образа 

Необходимо создать определение образа, соответствующее определению образа исходной версии образа. Вы можете просмотреть все сведения, необходимые для повторного создания определения образа в новой коллекции с помощью команды [AZ SIG Image-Definition Показать](/cli/azure/sig/image-definition#az-sig-image-definition-show).

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

Результат должен выглядеть следующим образом.

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Создайте новое определение образа в новой коллекции, используя сведения из выходных данных выше.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>Создание версии образа

Создайте версии с помощью команды [AZ Image Gallery создать-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create). Вам нужно ввести идентификатор управляемого образа, чтобы использовать его в качестве базового плана для создания версии образа. Для сведений об образах, содержащихся в группе ресурсов, можно использовать команду [az image list](/cli/azure/image?view#az-image-list). 

Допустимыми знаками для имени версии образа являются цифры и точки. Числа должны быть в диапазоне 32-битного целого числа. Формат: *основной номер версии*.*дополнительный номер версии*.*исправление*.

В этом примере версия нашего образа — *1.0.0* , и мы создадим 1 реплику в *юго-центральном регионе США* и 1 реплику в регионе *Восточной* части США, используя хранилище, избыточное в пределах зоны.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> Прежде чем использовать тот же управляемый образ для создания другой версии образа, необходимо дождаться завершения сборки и репликации версии образа.
>
> Вы также можете сохранить образ в хранилище класса Premium, добавив `--storage-account-type  premium_lrs`, или [хранилище, избыточное между зонами](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs), добавив `--storage-account-type  standard_zrs` при создании версии образа.
>

## <a name="next-steps"></a>Дальнейшие шаги

Создайте виртуальную машину на основе [обобщенной](vm-generalized-image-version-cli.md) или [специализированной](vm-specialized-image-version-cli.md) версии образа.

Кроме того, пробное использование [Azure Image Builder (Предварительная версия)](./linux/image-builder-overview.md) может помочь в автоматизации создания версий изображений, но его можно использовать для обновления и [создания новой версии образа из существующей версии образа](./linux/image-builder-gallery-update-image-version.md). 