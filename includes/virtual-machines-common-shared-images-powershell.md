---
title: включить файл
description: включить файл
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241209"
---
## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Вы также можете запустить Cloud Shell в [https://shell.azure.com/powershell](https://shell.azure.com/powershell)отдельной вкладке браузера, перейдя к . Выберите **Copy,** чтобы скопировать блоки кода, вставьте его в облачную оболочку и нажмите введите, чтобы запустить его.


## <a name="get-the-managed-image"></a>Получение управляемого образа

Список образов, доступных в группе ресурсов, можно просмотреть с помощью команды [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Зная имя образа и группу ресурсов, в которой он находится, можно снова выполнить команду `Get-AzImage`, чтобы получить объект образа и сохранить его в переменной для последующего использования. Этот пример получает образ с именем *myImage* из группы ресурсов myResourceGroup и присваивает его переменной *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Создание коллекции образов 

Коллекция образов является основным ресурсом, который позволяет обмен изображениями. Допустимыми знаками для имени коллекции являются прописные или строчные буквы, цифры и точки. Название галереи не может содержать тире. Имена коллекций должны быть уникальным в пределах вашей подписки. 

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

Определения изображений создают логическую группировку изображений. Они используются для управления информацией о версиях изображений, которые создаются в них. Имена определения изображения могут состоять из верхних или нижних букв, цифр, точек, тире и периодов. Для получения дополнительной информации о значениях, которые вы можете указать для определения изображения, [см.](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)

Создайте определение изображения с помощью [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). В этом примере используется образ коллекции с именем *myGalleryImage*.

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

Создайте версию изображения из управляемого изображения с помощью [New-AzGalleryImageVersion.](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) 

Допустимыми знаками для имени версии образа являются цифры и точки. Числа должны быть в диапазоне 32-битного целого числа. Формат: *MajorVersion*. *MinorVersion*. *Патч*.

В этом примере используется версия образа *1.0.0*, которая реплицируется в центры обработки данных в регионах *центрально-западная часть США* и *центрально-южная часть США*. При выборе целевых областей для репликации помните, что в качестве цели для репликации необходимо также включить область *исходного кода.*


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
> Вам нужно ждать, пока версия изображения полностью закончит строиться и реплицироваться, прежде чем вы сможете использовать то же управляемое изображение для создания другой версии изображения. 
>
> Вы также можете хранить свою версию изображения `-StorageAccountType Standard_ZRS` в [зоне избыточного хранения,](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) добавляя при создании версии изображения.
>


## <a name="share-the-gallery"></a>Поделитесь галереей

Мы рекомендуем вам поделиться доступом на уровне галереи изображений. Используйте адрес электронной почты и cmdlet [Get-AzADUser,](/powershell/module/az.resources/get-azaduser) чтобы получить идентификатор объекта для пользователя, а затем используйте [New-AzRoleAssignment,](/powershell/module/Az.Resources/New-AzRoleAssignment) чтобы дать им доступ к галерее. Замените пример alinne_montes@contoso.com электронной почты в этом примере своей собственной информацией.

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