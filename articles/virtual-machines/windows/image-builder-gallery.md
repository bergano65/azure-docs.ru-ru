---
title: Использование Azure Image Builder с коллекцией образов для виртуальных машин Windows (Предварительная версия)
description: Создание версий образа общей коллекции Azure с помощью Azure Image Builder и Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 48eff11facf0f1432534d61f003f61e6755caf33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869519"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Предварительная версия: создание образа Windows и его распространение в общую коллекцию образов 

В этой статье показано, как можно использовать построитель образов Azure и Azure PowerShell, чтобы создать версию образа в [общей коллекции образов](shared-image-galleries.md), а затем распространить образ глобально. Это можно также сделать с помощью [Azure CLI](../linux/image-builder-gallery.md).

Для настройки образа мы будем использовать JSON-шаблон. JSON-файл, который мы используем: [армтемплатевинсиг. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Мы будем скачивать и редактировать локальную версию шаблона, поэтому эта статья написана с помощью локального сеанса PowerShell.

Чтобы распространить образ в общую коллекцию образов, шаблон использует [шаредимаже](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) в качестве значения для `distribute` раздела шаблона.

Построитель образов Azure автоматически запускает Sysprep для подготовки образа, это универсальная команда Sysprep, которую можно [переопределить](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) при необходимости. 

Имейте в виду, сколько раз вы настраиваете слои. Вы можете выполнить команду Sysprep до 8 раз в одном образе Windows. После выполнения программы Sysprep 8 раз необходимо повторно создать образ Windows. Дополнительные сведения см. [в разделе ограничения на то, сколько раз можно запустить Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> Azure Image Builder сейчас находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Регистрация компонентов
Чтобы использовать Azure Image Builder во время предварительной версии, необходимо зарегистрировать новую функцию.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Проверьте состояние регистрации компонента.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

`RegistrationState` Дождитесь до перехода к следующему `Registered` шагу.

Проверьте регистрацию поставщиков. Убедитесь, что каждый `Registered`из них возвращает.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Если они не возвращаются `Registered`, используйте следующую команду для регистрации поставщиков:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Создание переменных

Мы будем использовать несколько фрагментов информации повторно, поэтому мы создадим некоторые переменные для хранения этих данных. Замените значения переменных, например `username` и `vmpassword`, собственными сведениями.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов и предоставьте разрешение Azure Image Builder для создания ресурсов в этой группе ресурсов.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>Создание Общей коллекции образов

Чтобы использовать построитель изображений с общей коллекцией изображений, необходимо иметь существующую коллекцию образов и определение образа. Построитель образов не будет создавать коллекцию изображений и определение изображения.

Если у вас еще нет определения коллекции и образа, начните с их создания. Сначала создайте коллекцию образов.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Скачивание и настройка шаблона

Скачайте шаблон JSON и настройте его с помощью переменных.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>Создание версии образа

Шаблон должен быть отправлен в службу, при этом будут скачаны зависимые артефакты, например сценарии, и сохраните их в промежуточной группе ресурсов с префиксом *IT_*.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Чтобы создать образ, необходимо вызвать команду Run для шаблона.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

Создание образа и его репликация в оба региона могут занять некоторое время. Дождитесь завершения этой части, прежде чем переходить к созданию виртуальной машины.

Сведения о вариантах автоматизации получения состояния сборки образа см. в [файле сведений](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) для этого шаблона на сайте GitHub.


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную машину на основе версии образа, созданной с помощью Azure Image Builder.

Получите созданную версию образа.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Создайте виртуальную машину во втором регионе, в котором выполнялась репликация образа.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Проверка настройки
Создайте удаленный рабочий стол подключение к виртуальной машине, используя имя пользователя и пароль, заданные при создании виртуальной машины. В виртуальной машине откройте командную строку и введите следующую команду:

```console
dir c:\
```

Вы должны увидеть каталог с именем `buildActions` , созданный во время настройки образа.


## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы хотите выполнить повторную настройку версии образа, чтобы создать новую версию того же образа, **пропустите этот шаг** и перейдите к разделу [Использование Azure Image Builder для создания другой версии образа](image-builder-gallery-update-image-version.md).


Это приведет к удалению созданного образа вместе со всеми остальными файлами ресурсов. Убедитесь, что вы завершили работу с этим развертыванием, прежде чем удалять ресурсы.

Сначала удалите шаблон группы ресурсов, иначе промежуточная группа ресурсов (*IT_*), используемая AIB, не будет очищена.

Получение ResourceID шаблона образа. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Удаление шаблона образа.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Удалите группу ресурсов.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Дальнейшие шаги

Сведения о том, как обновить созданную версию образа, см. в статье [Использование Azure Image Builder для создания другой версии образа](image-builder-gallery-update-image-version.md).
