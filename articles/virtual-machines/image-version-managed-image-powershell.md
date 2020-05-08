---
title: Перенос управляемого образа в коллекцию общих образов
description: Узнайте, как использовать Azure PowerShell для переноса управляемого образа в версию образа в общей коллекции образов.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e00538d1112492c5b7f9fc0f91c86df6d3500701
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796594"
---
# <a name="migrate-from-a-managed-image-to-a-shared-image-gallery-image"></a>Миграция из управляемого образа в общий образ коллекции образов

Если у вас есть управляемый образ, который вы хотите перенести в коллекцию общих образов, можно создать образ из коллекции общих образов непосредственно из управляемого образа. После тестирования нового образа можно удалить исходный управляемый образ. Можно также выполнить миграцию из управляемого образа в общую коллекцию образов с помощью [Azure CLI](image-version-managed-image-cli.md).

Изображения в коллекции образов имеют два компонента, которые будут созданы в этом примере:
- **Определение образа** содержит сведения о образе и требования для его использования. Сюда входит, является ли образ Windows или Linux, специализированным или обобщенным, заметками о выпуске, а также минимальным и максимальным требованиями к памяти. Это определение типа образа. 
- **Версия образа** — это то, что используется для создания виртуальной машины при использовании общей коллекции образов. В зависимости от требований для вашей среды, у вас может быть несколько версий образа. При создании виртуальной машины используется версия образа для создания новых дисков для виртуальной машины. Версии образов можно использовать несколько раз.


## <a name="before-you-begin"></a>Перед началом

Для работы с этой статьей необходим существующий управляемый образ. Если управляемый образ содержит диск данных, размер диска данных не может превышать 1 ТБ.

При работе со статьей по мере необходимости заменяйте имена групп ресурсов и виртуальных машин.

## <a name="get-the-gallery"></a>Получение коллекции

Вы можете вывести список всех коллекций и определений изображений по имени. Результаты представлены в формате `gallery\image definition\image version`.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Найдя нужную галерею, создайте переменную для последующего использования. Этот пример получает коллекцию с именем *мигаллери* в группе ресурсов *myResourceGroup* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Создание определения образа 

Определения образов создают логическую группировку для изображений. Они используются для управления сведениями об образе. Имена определений образов могут состоять из букв верхнего или нижнего регистра, цифр, точек, тире и точек. 

При создании определения образа убедитесь, что содержит все правильные сведения. Так как управляемые образы всегда обобщены, следует задать `-OsState generalized`. 

Дополнительные сведения о значениях, которые можно указать для определения изображения, см. в разделе [определения изображений](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Создайте определение образа с помощью команды [New-азгаллеримажедефинитион](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). В этом примере определение образа называется *мимажедефинитион*и предназначено для универсальной ОС Windows. Чтобы создать определение для образов с помощью ОС Linux, используйте `-OsType Linux`. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>Получение управляемого образа

Список образов, доступных в группе ресурсов, можно просмотреть с помощью команды [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Зная имя образа и группу ресурсов, в которой он находится, можно снова выполнить команду `Get-AzImage`, чтобы получить объект образа и сохранить его в переменной для последующего использования. Этот пример получает образ с именем *myImage* из группы ресурсов myResourceGroup и присваивает его переменной *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>Создание версии образа

Создайте версию образа из управляемого образа с помощью команды [New-азгаллеримажеверсион](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Допустимыми знаками для имени версии образа являются цифры и точки. Числа должны быть в диапазоне 32-битного целого числа. Формат: *majorversion*. *Minorversion*. *Исправление*.

В этом примере используется версия образа *1.0.0*, которая реплицируется в центры обработки данных в регионах *центрально-западная часть США* и *центрально-южная часть США*. При выборе целевых регионов для репликации Помните, что также необходимо включить *Исходный* регион в качестве цели для репликации. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

Репликация образа во все целевые регионы может занять некоторое время, поэтому мы создали задание, чтобы отслеживать ход выполнения. Чтобы просмотреть ход выполнения, введите `$job.State`.

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> Прежде чем использовать тот же управляемый образ для создания другой версии образа, необходимо дождаться завершения сборки и репликации версии образа. 
>
> Вы также можете сохранить образ в хранилище `-StorageAccountType Premium_LRS`премиун, добавив хранилище или [избыточное в зону](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) , добавив `-StorageAccountType Standard_ZRS` его при создании версии образа.
>

## <a name="delete-the-managed-image"></a>Удаление управляемого образа

Убедившись, что новая версия образа работает правильно, можно удалить управляемый образ.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

Убедившись, что репликация завершена, можно создать виртуальную машину из [обобщенного образа](vm-generalized-image-version-powershell.md).

