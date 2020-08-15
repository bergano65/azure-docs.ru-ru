---
title: Предварительный просмотр — создание версии образа, зашифрованной с помощью собственных ключей
description: Создайте версию образа в Общей коллекции образов с помощью управляемых клиентом ключей шифрования.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/11/2020
ms.author: cynthn
ms.openlocfilehash: 0d2b840b401dc90b332f91c93a9eda03d6643432
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245559"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Предварительный просмотр: Использование управляемых клиентом ключей для шифрования образов

Образы коллекции хранятся в виде управляемых дисков, поэтому они автоматически шифруются с помощью шифрования на стороне сервера. Для шифрования на стороне сервера используется 256-разрядное [шифрование AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). Это один из наиболее надежных блочных шифров, который также соответствует требованиям FIPS 140-2. Дополнительные сведения о криптографических модулях, лежащих в основе функции управляемых дисков Azure, см. в статье [Cryptography API: Next Generation](/windows/desktop/seccng/cng-portal) (API Cryptography: следующее поколение).

Вы можете использовать ключи, управляемые платформой, для шифрования образов, использования собственных ключей или одновременно для двойного шифрования. Если вы решили управлять шифрованием с помощью собственных ключей, вы можете указать *управляемый клиентом ключ*, который будет использоваться для шифрования и расшифровки всех дисков в образах. 

Для шифрования на стороне сервера с помощью управляемых клиентом ключей используется Azure Key Vault. Вы можете либо импортировать [ключи RSA](../key-vault/keys/hsm-protected-keys.md) в Key Vault, либо создать новые ключи RSA в Azure Key Vault.

## <a name="prerequisites"></a>Предварительные условия

В этой статье предполагается, что у вас уже есть набор шифрования дисков для использования в вашем образе.

- Сведения об использовании только управляемого клиентом ключа см. в разделе **Включение управляемых клиентом ключей с шифрованием на стороне сервера** с помощью [портал Azure](./windows/disks-enable-customer-managed-keys-portal.md) или [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset).

- Чтобы использовать управляемые платформой и управляемые клиентом ключи (для двойного шифрования), см. статью **Включение двойного шифрования при** хранении с помощью [портал Azure](./windows/disks-enable-double-encryption-at-rest-portal.md) или [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).
    > [!IMPORTANT]
    > Эту ссылку необходимо использовать [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) для доступа к портал Azure. Двойное шифрование неактивных компонентов в настоящее время не отображается в общедоступной портал Azure без использования ссылки.

## <a name="limitations"></a>Ограничения

При использовании ключей, управляемых клиентом, для шифрования образов из общей коллекции образов существует несколько ограничений.  

- Наборы ключей шифрования должны находиться в той же подписке и регионе, что и ваш образ.

- Нельзя совместно использовать образы, использующие управляемые клиентом ключи. 

- Нельзя реплицировать в другие регионы образы, в которых используются управляемые клиентом ключи.

- Если вы использовали собственные ключи для шифрования диска или образа, вы не сможете вернуться к использованию управляемых платформой ключей, чтобы шифровать эти диски или образы.


> [!IMPORTANT]
> Шифрование с помощью управляемых клиентом ключей в настоящее время находится на этапе общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Для использования общедоступной предварительной версии сначала необходимо зарегистрировать компонент.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Регистрация занимает несколько минут. Чтобы проверить состояние регистрации компонента, используйте командлет Get-AzProviderFeature.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Если RegistrationState возвращает значение Registered, можно переходить к следующему шагу.

Проверьте регистрацию поставщика. Убедитесь, что она возвращает значение `Registered`.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Если она не возвращает значение `Registered`, используйте следующую команду для регистрации поставщиков:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Чтобы задать набор шифрования диска для версии образа, используйте командлет [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) с параметром `-TargetRegion`. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @($region1)


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

Вы можете создать виртуальную машину на основе общей коллекции образов и использовать управляемые клиентом ключи для шифрования дисков. Синтаксис аналогичен используемому для создания [универсальной](vm-generalized-image-version-powershell.md) или [специализированной](vm-specialized-image-version-powershell.md) виртуальной машины из образа. Используйте расширенный набор параметров и добавьте `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` в конфигурацию виртуальной машины.

Для дисков данных при использовании [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) нужно добавить параметр `-DiskEncryptionSetId $setID`.


## <a name="cli"></a>CLI 

Для использования общедоступной предварительной версии сначала необходимо зарегистрировать компонент.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Проверьте состояние регистрации компонента.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Если этот параметр возвращает `"state": "Registered"`, можно перейти к следующему шагу.

Проверьте регистрацию.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Если регистрация не выполнена, выполните следующую команду:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Чтобы задать набор шифрования диска для версии образа, используйте команду [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) с параметром `--target-region-encryption`. Формат для `--target-region-encryption` представляет собой разделенный пробелами список ключей для шифрования дисков ОС и данных. Результат будет выглядеть так: `<encryption set for the OS disk>,<Lun number of the data disk>, <encryption set for the data disk>, <Lun number for the second data disk>, <encryption set for the second data disk>`. 

Если источником для диска ОС является управляемый диск или виртуальная машина, используйте `--managed-image`, чтобы указать источник для версии образа. В этом примере источником является управляемый образ с диском ОС, а также диском данных с LUN 0. Диск ОС будет зашифрован с помощью DiskEncryptionSet1, а диск данных — с помощью DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Если источником для диска ОС является моментальный снимок, используйте `--os-snapshot`, чтобы указать диск ОС. Если некоторые моментальные снимки диска данных также должны быть частью версии образа, добавьте их с помощью параметра `--data-snapshot-luns`, чтобы указать LUN, и параметра `--data-snapshots`, чтобы указать моментальные снимки.

В этом примере источниками являются моментальные снимки диска. Имеется диск ОС, а также диск данных с LUN 0. Диск ОС будет зашифрован с помощью DiskEncryptionSet1, а диск данных — с помощью DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Создание виртуальной машины

Вы можете создать виртуальную машину на основе общей коллекции образов и использовать управляемые клиентом ключи для шифрования дисков. Синтаксис аналогичен используемому для создания [универсальной](vm-generalized-image-version-cli.md) или [специализированной виртуальной машины](vm-specialized-image-version-cli.md) из образа. Достаточно лишь добавить параметр `--os-disk-encryption-set` с идентификатором набора шифрования. Для дисков данных добавьте `--data-disk-encryption-sets` с разделенным пробелами списком наборов шифрования диска для дисков данных.


## <a name="portal"></a>Портал

При создании версии образа на портале можно использовать вкладку **Шифрование** для ввода применения наборов шифрования хранилища.

> [!IMPORTANT]
> Чтобы использовать двойное шифрование, необходимо использовать эту ссылку [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) для доступа к портал Azure. Двойное шифрование неактивных компонентов в настоящее время не отображается в общедоступной портал Azure без использования ссылки.


1. На странице **Создание версии образа** выберите вкладку **Шифрование**.
2. В разделе **тип шифрования**выберите **Шифрование неактивных с помощью управляемого клиентом ключа** или **двойное шифрование с помощью управляемых платформой ключей и управляемых клиентом ключами**. 
3. Для каждого диска в образе выберите из раскрывающегося списка **набор шифрования дисков**, который нужно использовать. 

### <a name="create-the-vm"></a>Создание виртуальной машины

Вы можете создать виртуальную машину из версии образа и использовать управляемые клиентом ключи для шифрования дисков. При создании виртуальной машины на портале на вкладке **диски** выберите **Шифрование неактивных с помощью управляемых клиентом ключей** или **двойное шифрование с управляемыми платформой ключами и управляемым клиентом ключом** для **типа шифрования**. Затем можно выбрать набор шифрования из раскрывающегося списка.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [шифровании дисков на стороне сервера](./windows/disk-encryption.md).

Сведения о том, как предоставить сведения о плане покупки, см. в разделе [предоставление сведений о плане покупки Azure Marketplace при создании образов](marketplace-images.md).
