---
title: Шифрование управляемых дисков на стороне сервера - Azure CLI
description: Azure Storage защищает ваши данные, шифруя их в состоянии покоя, прежде чем сохранять их в кластерах хранения. Вы можете полагаться на управляемые корпорацией Майкрософт ключи для шифрования управляемых дисков или использовать управляемые клиентом ключи для управления шифрованием с помощью собственных ключей.
author: roygara
ms.date: 04/02/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: f7eb63d0bbdce86f4a7195430dc15d6873e9f6e6
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754313"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Шифрование дисков, управляемых Azure на стороне сервера

Управляемые Azure диски автоматически шифруют данные по умолчанию при сохранении их в облаке. Шифрование на стороне сервера защищает ваши данные и помогает выполнять свои обязательства по обеспечению безопасности и соответствию требованиям организации. Данные в управляемых дисках Azure прозрачно шифруются с помощью 256-битного [шифрования AES,](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)одного из самых сильных доступных шифров блоков, и соответствуют требованиям FIPS 140-2.   

Шифрование не влияет на производительность управляемых дисков. Дополнительные расходы на шифрование не утихат.

Для получения дополнительной информации о криптографических модулях, лежащих в основе управляемых дисков Azure, [см.](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Об управлении ключами шифрования

Вы можете положиться на управляемые платформой ключи для шифрования управляемого диска, или вы можете управлять шифрованием с помощью собственных ключей. Если вы решите управлять шифрованием с помощью собственных ключей, вы можете указать *ключ, управляемый клиентом,* чтобы использовать для шифрования и расшифровки всех данных на управляемых дисках. 

В следующих разделах более подробно описывают каждый из вариантов управления ключами.

## <a name="platform-managed-keys"></a>Ключи, управляемые платформой

По умолчанию управляемые диски используют управляемые платформой ключи шифрования. По состоянию на 10 июня 2017 года все новые управляемые диски, снимки, изображения и новые данные, записанные на существующие управляемые диски, автоматически шифруются на отдых с помощью управляемых платформами ключей.

## <a name="customer-managed-keys"></a>Ключи, управляемые клиентом

Вы можете управлять шифрованием на уровне каждого управляемого диска, с вашими собственными ключами. Шифрование на стороне сервера для управляемых дисков с ключами, управляемыми клиентом, предлагает интегрированный опыт работы с Azure Key Vault. Вы можете импортировать [ключи RSA](../../key-vault/key-vault-hsm-protected-keys.md) в Key Vault или создавать новые клавиши RSA в Azure Key Vault. 

Управляемые диски Azure обрабатывают шифрование и расшифровку полностью прозрачным способом с помощью [шифрования конвертов.](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique) Он шифрует данные с помощью ключа шифрования данных [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (DEK), который, в свою очередь, защищен с помощью ключей. Служба хранения генерирует ключи шифрования данных и шифрует их ключами, управляемыми клиентами, с помощью шифрования RSA. Шифрование конверта позволяет периодически поворачивать (изменять) ключи в соответствии с вашими правилами соответствия, не влияя на ваши ВМ. При повороте ключей служба хранения повторно шифрует ключи шифрования данных новыми ключами, управляемыми клиентом. 

Вы должны предоставить доступ к управляемым дискам в вашем Ключевом Убежище, чтобы использовать ваши ключи для шифрования и расшифровки DEK. Это позволяет полностью контролировать свои данные и ключи. Вы можете отключить ключи или отозвать доступ к управляемым дискам в любое время. Вы также можете провести аудит использования ключей шифрования с помощью мониторинга Azure Key Vault, чтобы убедиться, что только управляемые диски или другие надежные службы Azure получают доступ к вашим ключам.

Для премиальных SSD-навистых, стандартных SSD-наготовителя химаху до разогнана: при отключении или удалении ключа любые ВМ с дисками с использованием этого ключа автоматически выключаются. После этого, VMs не будет пригодным для удовкостных, если ключ включен снова или вы назначите новый ключ.

Для ультрадисков, когда вы отключаете или удалите ключ, любые VMs с ультра дисками с помощью ключа не будут автоматически выключаться. После того как вы делавы и перезапустить ВМ, то диски перестанут использовать ключ, а затем VMs не вернется в Интернете. Чтобы вернуть в интернет ввоз в систему ВМ, необходимо назначить новый ключ или включить существующий ключ.

На следующей диаграмме показано, как управляемые диски используют Azure Active Directory и Azure Key Vault для выполнения запросов с помощью ключа, управляемого клиентом:

![Управляемый диск и управляемый клиентом рабочий процесс ключей. Админ создает Хранилище ключей Azure, затем создает набор шифрования дисков и настраивает набор шифрования диска. Набор связан с VM, что позволяет диску использовать Azure AD для проверки подлинности](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


В следующем списке диаграмма объясняется еще более подробно:

1. Администратор хранилища ключей Azure создает ресурсы хранилища ключей.
1. Админ-админ ключа либо импортирует свои ключи RSA в Key Vault, либо генерирует новые клавиши RSA в Key Vault.
1. Этот администратор создает экземпляр ресурса набора шифрования дисков, упомяное идентификатор убежища azure Key Vault и URL-адрес ключа. Набор шифрования дисков — это новый ресурс, созданный для упрощения управления ключами для управляемых дисков. 
1. При создании набора шифрования дисков в Azure Active Directory (AD) создается [набор управляемых данных, назначенных системой,](../../active-directory/managed-identities-azure-resources/overview.md) и связанс с набором шифрования дисков. 
1. Затем администратор хранилища ключей Azure предоставляет управляемое разрешение на выполнение операций в хранилище ключей.
1. Пользователь VM создает диски, связывая их с набором шифрования дисков. Пользователь VM может также включить шифрование на стороне сервера с управляемыми клиентами ключами для существующих ресурсов, связывая их с набором шифрования диска. 
1. Управляемые диски используют управляемый итог для отправки запросов в Хранилище ключей Azure.
1. Для чтения или записи данных управляемые диски отправляют запросы в Azure Key Vault для шифрования (обертывания) и расшифровки (распаковки) ключа шифрования данных для выполнения шифрования и расшифровки данных. 

Чтобы отозвать доступ к ключам, управляемым клиентами, [см.](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) Отмена доступа фактически блокирует доступ ко всем данным в учетной записи хранилища, так как ключ шифрования недоступен Azure Storage.

### <a name="supported-regions"></a>Поддерживаемые регионы

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Ограничения

На данный момент, управляемые клиентом ключи имеют следующие ограничения:

- Если эта функция включена для вашего диска, вы не можете отключить его.
    Если вам нужно обойти это, вы должны [скопировать все данные](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) на совершенно другой управляемый диск, который не использует ключей, управляемых клиентом.
- Поддерживаются только ["мягкие" и "жесткие" клавиши RSA](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) размером 2048, никаких других клавиш или размеров.
- Диски, созданные из пользовательских изображений, которые шифруются с помощью шифрования на стороне сервера и ключей, управляемых клиентом, должны быть зашифрованы с помощью одних и тех же ключей, управляемых клиентом, и должны находиться в одной подписке.
- Снимки, созданные на дисках, которые шифруются с помощью шифрования на стороне сервера и ключей, управляемых клиентом, должны быть зашифрованы с помощью тех же ключей, управляемых клиентом.
- Пользовательские изображения, зашифрованные с помощью шифрования на стороне сервера и ключей, управляемых клиентом, не могут быть использованы в общей галерее изображений.
- Все ресурсы, связанные с ключами, управляемыми клиентом (Azure Key Vaults, наборы шифрования дисков, виможно, диски и моментальные снимки), должны находиться в одной подписке и регионе.
- Диски, снимки и изображения, зашифрованные с помощью ключей, управляемых клиентом, не могут перейти на другую подписку.
- Если вы используете портал Azure для создания набора шифрования дисков, вы не можете использовать снимки на данный момент.
- Управляемые диски, зашифрованные с помощью ключей, управляемых клиентом, также не могут быть зашифрованы с помощью azure Disk Encryption.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Настройка хранилища ключей Azure и DiskEncryptionSet

1. Обязательно установите последнюю версию [Azure CLI](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index).

1. Создайте экземпляр Убежища ключей Azure и ключа шифрования.

    При создании экземпляра Key Vault необходимо включить защиту от мягкого удаления и очистки. Мягкое удаление гарантирует, что Key Vault содержит удаленный ключ для данного периода удержания (90 дней по умолчанию). Защита очистки гарантирует, что удаленный ключ не может быть удален навсегда до тех пор, пока не пройдет период хранения. Эти параметры защищают вас от потери данных из-за случайного удаления. Эти настройки являются обязательными при использовании Key Vault для шифрования управляемых дисков.

    > [!IMPORTANT]
    > Не регистрируй область, если это так, у вас могут возникнуть проблемы при назначении дополнительных дисков ресурсу на портале Azure.

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

1.    Создайте экземпляр DiskEncryptionSet. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Предоставите ресурсу DiskEncryptionSet доступ к хранилищу ключей. 

        > [!NOTE]
        > Для azure может потребоваться несколько минут, чтобы создать идентификацию вашего DiskEncryptionSet в вашем active Directory Azure. Если вы получаете ошибку типа "Не удается найти объект Active Directory" при запуске следующей команды, подождите несколько минут и повторите попытку.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Создание VM с помощью изображения Marketplace, шифрования ОС и дисков данных с помощью ключей, управляемых клиентом

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


#### <a name="encrypt-existing-unattached-managed-disks"></a>Шифрование существующих неприсоединенных управляемых дисков 

Существующие диски не должны быть прикреплены к бегущему VM, чтобы шифровать их с помощью следующего сценария:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Создание виртуального набора масштабов машины с использованием изображения Marketplace, шифрования ОС и дисков данных с помощью ключей, управляемых клиентом

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

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Создайте пустой диск, зашифрованный с помощью шифрования на стороне сервера с ключами, управляемыми клиентом, и прикрепите его к VM

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

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Измените ключ DiskEncryptionSet, чтобы повернуть ключ для всех ресурсов, ссылающихся на DiskEncryptionSet

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Найти состояние шифрования диска на стороне сервера

```azurecli

az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv

```

> [!IMPORTANT]
> Управляемые клиентами ключи опираются на управляемые идентификаторы для ресурсов Azure, функцию Active Directory Azure (Azure AD). При настройке ключей, управляемых клиентом, управляемая идентификация автоматически назначается вашим ресурсам под крышкой. Если впоследствии вы переведете подписную подписку, группу ресурсов или управляемый диск из одного каталога Azure AD в другой, управляемая идентификация, связанная с управляемыми дисками, не передается новому арендатору, поэтому ключи, управляемые клиентом, могут больше не работать. Для получения дополнительной [информации см. Передача подписки между каталогами Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Управляемые клиентами ключи опираются на управляемые идентификаторы для ресурсов Azure, функцию Active Directory Azure (Azure AD). При настройке ключей, управляемых клиентом, управляемая идентификация автоматически назначается вашим ресурсам под крышкой. Если впоследствии вы переведете подписную подписку, группу ресурсов или управляемый диск из одного каталога Azure AD в другой, управляемая идентификация, связанная с управляемыми дисками, не передается новому арендатору, поэтому ключи, управляемые клиентом, могут больше не работать. Для получения дополнительной [информации см. Передача подписки между каталогами Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Шифрование на стороне сервера против шифрования дисков Azure

[Лазурное шифрование дисков для виртуальных машин и виртуальных наборов машин](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) использует функцию [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) Windows и функцию [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) Linux для шифрования управляемых дисков с управляемыми клиентом ключами в гостевом VM.  Шифрование на стороне сервера с ключами, управляемыми клиентом, улучшает ADE, позволяя использовать любые типы ОС и изображения для ви-х годов путем шифрования данных в службе хранения данных.

## <a name="next-steps"></a>Дальнейшие действия

- [Исследуйте шаблоны менеджера ресурсов Azure для создания зашифрованных дисков с ключами, управляемыми клиентом](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Что такое хранилище ключей Azure?](../../key-vault/key-vault-overview.md)
- [Репликат машины с клиентами-управляемыми ключами с включенными дисками](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Настройка аварийного восстановления виртуальных машин VMware в Azure с помощью PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Настройка аварийного восстановления виртуальных машин Hyper-V в Azure с помощью PowerShell и Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
