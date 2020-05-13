---
title: Шифрование управляемых дисков Azure на стороне сервера — Azure CLI
description: Служба хранилища Azure защищает ваши данные путем шифрования неактивных данных перед их сохранением в кластерах хранилища. Для шифрования управляемых дисков можно использовать ключи, управляемые корпорацией Майкрософт, или ключи, управляемые клиентом, для управления шифрованием с помощью собственных ключей.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: a9266f20d276b0b8d315cc83cd96fc235c79e773
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198957"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Шифрование управляемых дисков Azure на стороне сервера

Управляемые диски Azure автоматически шифруют данные по умолчанию при сохранении их в облаке. Шифрование на стороне сервера защищает ваши данные и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации. Данные на управляемых дисках Azure шифруются прозрачно с помощью 256-битного [шифрования AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), одним из наиболее подданных блоков блочных шифров и совместимым с FIPS 140-2.   

Шифрование не влияет на производительность управляемых дисков. Дополнительные затраты на шифрование не взимается.

Дополнительные сведения о криптографических модулях, лежащих в основе управляемых дисков Azure, см. в разделе [API шифрования: следующее поколение.](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Сведения об управлении ключами шифрования

Для шифрования управляемого диска можно использовать ключи, управляемые платформой, или можно управлять шифрованием с помощью собственных ключей. Если вы решили управлять шифрованием с помощью собственных ключей, можно указать ключ, *управляемый клиентом* , который будет использоваться для шифрования и расшифровки всех данных на управляемых дисках. 

В следующих разделах каждый из параметров управления ключами описан более подробно.

## <a name="platform-managed-keys"></a>Ключи, управляемые платформой

По умолчанию управляемые диски используют управляемые платформой ключи шифрования. Начиная с 10 июня 2017, все новые управляемые диски, моментальные снимки, образы и новые данные, записанные на существующие управляемые диски, автоматически шифруются с помощью управляемых платформой ключей.

## <a name="customer-managed-keys"></a>Ключи, управляемые клиентом

Вы можете управлять шифрованием на уровне каждого управляемого диска с помощью собственных ключей. Шифрование на стороне сервера для управляемых дисков с ключами, управляемыми клиентом, обеспечивает интегрированную работу с Azure Key Vault. Вы можете либо импортировать [ключи RSA](../../key-vault/keys/hsm-protected-keys.md) в Key Vault, либо создать новые ключи rsa в Azure Key Vault. 

Управляемые диски Azure обрабатывают шифрование и расшифровку полностью прозрачным способом с помощью [шифрования конвертов](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Он шифрует данные с помощью ключа шифрования данных на основе [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (DEK), который, в свою очередь, защищен с помощью ключей. Служба хранилища создает ключи шифрования данных и шифрует их с помощью управляемых клиентом ключей, используя шифрование RSA. Шифрование конверта позволяет периодически менять ключи в соответствии с политиками соответствия, не влияя на виртуальные машины. При вращении ключей служба хранилища повторно шифрует ключи шифрования данных с помощью новых ключей, управляемых клиентом. 

Вы должны предоставить доступ к управляемым дискам в Key Vault, чтобы использовать ключи для шифрования и расшифровки DEK. Это обеспечивает полный контроль над данными и ключами. Вы можете отключить ключи или отозвать доступ к управляемым дискам в любое время. Кроме того, можно проводить аудит использования ключа шифрования с Azure Key Vault мониторинга, чтобы обеспечить доступ к ключам только управляемых дисков или других доверенных служб Azure.

Для твердотельных накопителей Premium, стандартных твердотельных накопителей и жестких дисков уровня "Стандартный": при отключении или удалении ключа все виртуальные машины с дисками, использующие этот ключ, автоматически завершат работу. После этого виртуальные машины не будут использоваться, пока ключ не будет включен снова или не назначен новый ключ.

Если вы отключаете или удаляете ключ для Ultra Disks, все виртуальные машины с Ultra Disks, использующие этот ключ, не будут автоматически завершаться. После отмены распределения и перезапуска виртуальных машин диски будут прекращаться с помощью ключа, а виртуальные машины не будут возвращаться в оперативный режим. Чтобы вернуть виртуальные машины в оперативный режим, необходимо назначить новый ключ или включить существующий ключ.

На следующей схеме показано, как управляемые диски используют Azure Active Directory и Azure Key Vault для выполнения запросов с помощью управляемого клиентом ключа:

![Управляемый диск и рабочий процесс с управляемыми клиентом ключами. Администратор создает Azure Key Vault, создает набор шифрования дисков и настраивает набор шифрования дисков. Набор связан с виртуальной машиной, что позволяет диску использовать Azure AD для проверки подлинности.](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


В следующем списке приведено более подробное описание схемы:

1. Администратор Azure Key Vault создает ресурсы хранилища ключей.
1. Администратор хранилища ключей импортирует ключи RSA в Key Vault или создает новые ключи RSA в Key Vault.
1. Этот администратор создает экземпляр ресурса для набора шифрования диска, указывая идентификатор Azure Key Vault и URL-адрес ключа. Набор шифрования дисков — это новый ресурс, который появился для упрощения управления ключами для управляемых дисков. 
1. При создании набора шифрования дисков [назначенное системой управляемое удостоверение](../../active-directory/managed-identities-azure-resources/overview.md) создается в Azure Active Directory (AD) и связывается с набором шифрования дисков. 
1. Затем администратор хранилища ключей Azure предоставляет управляемому удостоверению разрешение на выполнение операций в хранилище ключей.
1. Пользователь виртуальной машины создает диски, связывая их с набором шифрования дисков. Пользователь виртуальной машины может также включить шифрование на стороне сервера с помощью управляемых клиентом ключей для существующих ресурсов, связав их с набором шифрования дисков. 
1. Управляемые диски используют управляемое удостоверение для отправки запросов в Azure Key Vault.
1. Для чтения или записи данных управляемые диски отправляют запросы в Azure Key Vault для шифрования (переноса) и расшифровки (распаковки) ключа шифрования данных для выполнения шифрования и расшифровки данных. 

Чтобы отозвать доступ к ключам, управляемым клиентом, см. статью [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) и [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Отзыв доступа блокирует доступ ко всем данным в учетной записи хранения, так как ключ шифрования недоступен для службы хранилища Azure.

### <a name="supported-regions"></a>Поддерживаемые регионы

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Ограничения

Сейчас ключи, управляемые клиентом, имеют следующие ограничения.

- Если эта функция включена для диска, отключить ее нельзя.
    Если необходимо обойти эту ошибку, необходимо [скопировать все данные](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) на совершенно другой управляемый диск, не использующий ключи, управляемые клиентом.
- Поддерживаются только [«мягкие» и «жесткие» ключи RSA](../../key-vault/keys/about-keys.md) размером 2048, а другие ключи и размеры отсутствуют.
- Диски, созданные на основе пользовательских образов, зашифрованных с помощью шифрования на стороне сервера и ключей, управляемых клиентом, должны быть зашифрованы с помощью тех же самых управляемых клиентом ключей и должны находиться в одной подписке.
- Моментальные снимки, созданные на основе дисков, зашифрованных с помощью шифрования на стороне сервера и ключей, управляемых клиентом, должны быть зашифрованы с помощью тех же управляемых клиентом ключей.
- Все ресурсы, связанные с ключами, управляемыми клиентом (хранилища ключей Azure, наборы шифрования дисков, виртуальные машины, диски и моментальные снимки), должны находиться в одной подписке и регионе.
- Диски, моментальные снимки и образы, зашифрованные с помощью управляемых клиентом ключей, не могут переместиться в другую подписку.
- Если вы используете портал Azure для создания набора шифрования диска, использовать моментальные снимки сейчас нельзя.
- Управляемые диски, зашифрованные с помощью ключей, управляемых клиентом, также не шифруются с помощью шифрования дисков Azure.
- Сведения об использовании управляемых клиентом ключей с общими галереями образов см. в разделе [Предварительная версия: использование управляемых клиентом ключей для шифрования образов](../image-version-encryption.md).

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Настройка Azure Key Vault и Дискенкриптионсет

1. Обязательно установите последнюю версию [Azure CLI](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index).

1. Создайте экземпляр Azure Key Vault и ключ шифрования.

    При создании экземпляра Key Vault необходимо включить защиту с обратимым удалением и очисткой. Обратимое удаление гарантирует, что Key Vault будет содержать удаленный ключ для заданного срока хранения (90 день по умолчанию). Очистка защиты гарантирует, что удаленный ключ нельзя удалить навсегда, пока не истечет срок хранения. Эти параметры защищают от потери данных из-за случайного удаления. Эти параметры являются обязательными при использовании Key Vault для шифрования управляемых дисков.

    > [!IMPORTANT]
    > Не выделять регион. в этом случае могут возникнуть проблемы при назначении дополнительных дисков ресурсу в портал Azure.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Создайте экземпляр Дискенкриптионсет. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Предоставьте ресурсу Дискенкриптионсет доступ к хранилищу ключей. 

        > [!NOTE]
        > Создание удостоверения Дискенкриптионсет в Azure Active Directory может занять несколько минут Azure. Если при выполнении следующей команды появляется сообщение об ошибке, например "не удается найти объект Active Directory", подождите несколько минут и повторите попытку.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Создание виртуальной машины с помощью образа Marketplace, шифрование дисков операционной системы и данных с помощью управляемых клиентом ключей

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-managed-disks"></a>Шифрование существующих управляемых дисков 

Существующие диски не должны быть подключены к работающей виртуальной машине, чтобы их можно было зашифровать с помощью следующего скрипта:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Создание масштабируемого набора виртуальных машин с помощью образа Marketplace, шифрование дисков операционной системы и данных с помощью управляемых клиентом ключей

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Создайте пустой диск, зашифрованный с помощью шифрования на стороне сервера, с ключами, управляемыми клиентом, и подключите его к виртуальной машине.

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=westcentralus
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Измените ключ Дискенкриптионсет, чтобы повернуть ключ для всех ресурсов, ссылающихся на Дискенкриптионсет

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Поиск состояния шифрования диска на стороне сервера

```azurecli

az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv

```

> [!IMPORTANT]
> Управляемые клиентом ключи используют управляемые удостоверения для ресурсов Azure, функции Azure Active Directory (Azure AD). При настройке управляемых пользователем ключей управляемое удостоверение автоматически назначается вашим ресурсам. При последующем перемещении подписки, группы ресурсов или управляемого диска из одного каталога Azure AD в другой управляемое удостоверение, связанное с управляемыми дисками, не передается в новый клиент, поэтому управляемые клиентом ключи могут перестать работать. Дополнительные сведения см. [в статье передача подписки между каталогами Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Управляемые клиентом ключи используют управляемые удостоверения для ресурсов Azure, функции Azure Active Directory (Azure AD). При настройке управляемых пользователем ключей управляемое удостоверение автоматически назначается вашим ресурсам. При последующем перемещении подписки, группы ресурсов или управляемого диска из одного каталога Azure AD в другой управляемое удостоверение, связанное с управляемыми дисками, не передается в новый клиент, поэтому управляемые клиентом ключи могут перестать работать. Дополнительные сведения см. [в статье передача подписки между каталогами Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Шифрование на стороне сервера и шифрование дисков Azure

[Шифрование дисков Azure для виртуальных машин и масштабируемых наборов виртуальных машин](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) использует функцию [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) в Windows и функцию [DM-](https://en.wikipedia.org/wiki/Dm-crypt) Encryption в Linux для шифрования управляемых дисков с помощью управляемых клиентом ключей на гостевой виртуальной машине.  Шифрование на стороне сервера с помощью управляемых клиентом ключей улучшает работу с ADE, позволяя использовать любые типы ОС и образы для виртуальных машин путем шифрования данных в службе хранилища.

## <a name="next-steps"></a>Дальнейшие действия

- [Изучите шаблоны Azure Resource Manager для создания зашифрованных дисков с помощью управляемых клиентом ключей](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Что такое хранилище ключей Azure?](../../key-vault/general/overview.md)
- [Репликация компьютеров с помощью дисков с поддержкой ключей, управляемых клиентом](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Настройка аварийного восстановления виртуальных машин VMware в Azure с помощью PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Настройка аварийного восстановления виртуальных машин Hyper-V в Azure с помощью PowerShell и Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
