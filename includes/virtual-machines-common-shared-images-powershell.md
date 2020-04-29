---
title: Включить имя файла
description: включить файл
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2020
ms.locfileid: "66241209"
---
## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.


## <a name="get-the-managed-image"></a>Получение управляемого образа

Список образов, доступных в группе ресурсов, можно просмотреть с помощью команды [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Зная имя образа и группу ресурсов, в которой он находится, можно снова выполнить команду `Get-AzImage`, чтобы получить объект образа и сохранить его в переменной для последующего использования. Этот пример получает образ с именем *myImage* из группы ресурсов myResourceGroup и присваивает его переменной *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Создание коллекции образов 

Коллекция образов является основным ресурсом, который позволяет обмен изображениями. Допустимыми знаками для имени коллекции являются прописные или строчные буквы, цифры и точки. Имя коллекции не может содержать дефисы. Имена коллекций должны быть уникальным в пределах вашей подписки. 

Создайте коллекцию образов, используя команду [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). В следующем примере показано, как создать коллекцию с именем *myGallery* в группе ресурсов *myGalleryRG*.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Создание определения образа 

Определения образов создают логическую группировку для изображений. Они используются для управления сведениями о версиях образов, созданных в них. Имена определений образов могут состоять из букв верхнего или нижнего регистра, цифр, точек, тире и точек. Дополнительные сведения о значениях, которые можно указать для определения изображения, см. в разделе [определения изображений](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Создайте определение образа с помощью команды [New-азгаллеримажедефинитион](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). В этом примере используется образ коллекции с именем *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
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
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

Репликация образа во все целевые регионы может занять некоторое время, поэтому мы создали задание, чтобы отслеживать ход выполнения. Чтобы просмотреть ход выполнения задания, введите `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Прежде чем использовать тот же управляемый образ для создания другой версии образа, необходимо дождаться завершения сборки и репликации версии образа. 
>
> Можно также сохранить версию образа в хранилище, [избыточном](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) в виде зоны `-StorageAccountType Standard_ZRS` , путем добавления при создании версии образа.
>


## <a name="share-the-gallery"></a>Предоставление общего доступа к коллекции

Рекомендуется предоставлять общий доступ на уровне коллекции образов. Используйте адрес электронной почты и командлет [Get-азадусер](/powershell/module/az.resources/get-azaduser) , чтобы получить идентификатор объекта для пользователя, а затем используйте [New-азролеассигнмент](/powershell/module/Az.Resources/New-AzRoleAssignment) , чтобы предоставить им доступ к коллекции. Замените пример адреса электронной почты alinne_montes@contoso.com в этом примере собственными сведениями.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```