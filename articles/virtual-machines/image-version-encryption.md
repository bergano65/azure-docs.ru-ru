---
title: Предварительный просмотр — создание версии образа, зашифрованной с помощью собственных ключей
description: Создание версии образа в общей коллекции образов с помощью управляемых клиентом ключей шифрования.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/06/2020
ms.author: cynthn
ms.openlocfilehash: aeacfdc07e5349dfce45b209da1d78bddf870f33
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83269586"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Предварительная версия: использование управляемых клиентом ключей для шифрования образов

Образы коллекции хранятся в виде управляемых дисков, поэтому они автоматически шифруются с помощью шифрования на стороне сервера. Шифрование на стороне сервера использует 256-разрядное [шифрование AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), один из наиболее подходящего блочного шифра, совместимый с FIPS 140-2. Дополнительные сведения о криптографических модулях, лежащих в основе управляемых дисков Azure, см. в разделе [API шифрования: следующее поколение.](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

Вы можете использовать ключи, управляемые платформой, для шифрования образов, а также управлять шифрованием с помощью собственных ключей. Если вы решили управлять шифрованием с помощью собственных ключей, можно указать ключ, *управляемый клиентом* , который будет использоваться для шифрования и расшифровки всех дисков в ваших образах. 

Шифрование на стороне сервера с помощью управляемых клиентом ключей использует Azure Key Vault. Вы можете либо импортировать [ключи RSA](../key-vault/keys/hsm-protected-keys.md) в Key Vault, либо создать новые ключи rsa в Azure Key Vault.

Чтобы использовать управляемые пользователем ключи для образов, сначала необходимо Azure Key Vault. Затем создается набор шифрования дисков. Затем набор шифрования диска используется при создании версий образа.

Дополнительные сведения о создании и использовании наборов шифрования дисков см. в разделе [управляемые клиентом ключи](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys).

## <a name="limitations"></a>Ограничения

При использовании управляемых пользователем ключей для шифрования образов общей коллекции образов существует несколько ограничений.  

- Наборы ключей шифрования должны находиться в той же подписке и регионе, что и образ.

- Нельзя совместно использовать образы, использующие управляемые клиентом ключи. 

- Нельзя реплицировать образы, в которых используются управляемые клиентом ключи, в другие регионы.

- После того как вы использовали собственные ключи для шифрования диска или образа, вы не сможете вернуться к использованию ключей, управляемых платформой, для шифрования этих дисков или образов.


> [!IMPORTANT]
> Шифрование с помощью управляемых клиентом ключей в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Для общедоступной предварительной версии сначала необходимо зарегистрировать функцию.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Для завершения регистрации потребуется несколько минут. Чтобы проверить состояние регистрации компонента, используйте команду Get-Азпровидерфеатуре.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Когда RegistrationState возвращает значение Registered, можно перейти к следующему шагу.

Проверьте регистрацию поставщика. Убедитесь, что он возвращает `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Если они не возвращаются `Registered` , используйте следующую команду для регистрации поставщиков:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Чтобы указать для параметра шифрование диска значение для версии образа, используйте командлет [New-азгаллеримажедефинитион](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) с `-TargetRegion` параметром. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @{$region1}


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Создание виртуальной машины

Вы можете создать виртуальную машину из общей коллекции образов и использовать управляемые клиентом ключи для шифрования дисков. Синтаксис аналогичен созданию [обобщенной](vm-generalized-image-version-powershell.md) или [специализированной](vm-specialized-image-version-powershell.md) виртуальной машины из образа, необходимо использовать набор расширенных параметров и добавить его `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` в конфигурацию виртуальной машины.

Для дисков данных необходимо добавить `-DiskEncryptionSetId $setID` параметр при использовании командлета [Add-азвмдатадиск](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Для общедоступной предварительной версии сначала необходимо зарегистрировать функцию.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Проверьте состояние регистрации компонента.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Когда это возвращается `"state": "Registered"` , можно перейти к следующему шагу.

Проверьте регистрацию.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Если он не зарегистрирован, выполните следующую команду:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Чтобы указать для параметра шифрование диска значение для версии образа, используйте команду [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create) с `--target-region-encryption` параметром. Формат для `--target-region-encryption` — это разделенный пробелами список ключей для шифрования дисков операционной системы и данных. Результат будет выглядеть так: `<encryption set for the OS disk>,<Lun number of the data disk>, <encryption set for the data disk>, <Lun number for the second data disk>, <encryption set for the second data disk>`. 

Если источником для диска операционной системы является управляемый диск или виртуальная машина, используйте `--managed-image` для указания источника версии образа. В этом примере источником является управляемый образ с диском операционной системы, а также диском данных в LUN 0. Диск ОС будет зашифрован с помощью DiskEncryptionSet1, а диск данных будет зашифрован с помощью DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Если источником для диска операционной системы является моментальный снимок, используйте `--os-snapshot` для указания диска операционной системы. Если имеются моментальные снимки диска данных, которые также должны быть частью версии образа, добавьте их с помощью, `--data-snapshot-luns` чтобы указать LUN, и `--data-snapshots` Укажите эти моментальные снимки.

В этом примере источниками являются моментальные снимки диска. Имеется диск операционной системы, а также диск данных в LUN 0. Диск ОС будет зашифрован с помощью DiskEncryptionSet1, а диск данных будет зашифрован с помощью DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot"
   --data-snapshot-luns 0
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot"
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Создание виртуальной машины

Вы можете создать виртуальную машину из общей коллекции образов и использовать управляемые клиентом ключи для шифрования дисков. Синтаксис аналогичен созданию [обобщенной](vm-generalized-image-version-cli.md) или [специализированной](vm-specialized-image-version-cli.md) виртуальной машины из образа. необходимо просто добавить `--os-disk-encryption-set` параметр с идентификатором набора шифрования. Для дисков данных добавьте `--data-disk-encryption-sets` список наборов шифрования дисков для дисков данных с разделителями-пробелами.


## <a name="portal"></a>Портал

При создании версии образа на портале можно использовать вкладку **Шифрование** , чтобы ввести сведения о наборах шифрования хранилища.

1. На странице **Создание версии образа** выберите вкладку **Шифрование** .
2. В разделе **тип шифрования**выберите **Шифрование неактивных ключей с помощью управляемого клиентом ключа**. 
3. Для каждого диска в образе выберите **набор шифрования дисков** для использования из раскрывающегося списка. 

### <a name="create-the-vm"></a>Создание виртуальной машины

Вы можете создать виртуальную машину из общей коллекции образов и использовать управляемые клиентом ключи для шифрования дисков. При создании виртуальной машины на портале на вкладке **диски** выберите **Шифрование неактивных ключей с ключами, управляемыми клиентом** , для **типа шифрования**. Затем можно выбрать набор шифрования из раскрывающегося списка.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [шифровании дисков на стороне сервера](/windows/disk-encryption.md).
