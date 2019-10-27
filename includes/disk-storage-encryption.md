---
title: включение файла
description: включение файла
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cffb9efaf828b3793133143e97c0fc87f840df42
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966298"
---
Управляемые диски Azure автоматически шифруют данные по умолчанию при сохранении их в облаке. Шифрование на стороне сервера защищает ваши данные и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации. Данные на управляемых дисках Azure шифруются прозрачно с помощью 256-битного [шифрования AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), одним из наиболее подданных блоков блочных шифров и совместимым с FIPS 140-2.   

Шифрование не влияет на производительность управляемых дисков. Дополнительные затраты на шифрование не взимается.

Дополнительные сведения о криптографических модулях, лежащих в основе управляемых дисков Azure, см. в разделе [API шифрования: следующее поколение.](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Сведения об управлении ключами шифрования

Для шифрования управляемого диска можно использовать ключи, управляемые платформой, или можно управлять шифрованием с помощью собственных ключей (общедоступная Предварительная версия). Если вы решили управлять шифрованием с помощью собственных ключей, можно указать ключ, *управляемый клиентом* , который будет использоваться для шифрования и расшифровки всех данных на управляемых дисках. 

В следующих разделах каждый из параметров управления ключами описан более подробно.

## <a name="platform-managed-keys"></a>Ключи, управляемые платформой

По умолчанию управляемые диски используют управляемые платформой ключи шифрования. Начиная с 10 июня 2017, все новые управляемые диски, моментальные снимки, образы и новые данные, записанные на существующие управляемые диски, автоматически шифруются с помощью ключей, управляемых платформой. 

## <a name="customer-managed-keys-public-preview"></a>Ключи, управляемые клиентом (общедоступная Предварительная версия)

Вы можете управлять шифрованием на уровне каждого управляемого диска с помощью собственных ключей. Шифрование на стороне сервера для управляемых дисков с ключами, управляемыми клиентом, обеспечивает интегрированную работу с Azure Key Vault. Вы можете либо импортировать [ключи RSA](../articles/key-vault/key-vault-hsm-protected-keys.md) в Key Vault, либо создать новые ключи rsa в Azure Key Vault. Управляемые диски Azure обрабатывают шифрование и расшифровку полностью прозрачным способом с помощью [шифрования конвертов](../articles/storage/common/storage-client-side-encryption.md#encryption-via-the-envelope-technique). Он шифрует данные с помощью ключа шифрования данных на основе [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (DEK), который, в свою очередь, защищен с помощью ключей. Чтобы использовать ключи для шифрования и расшифровки DEK, необходимо предоставить доступ в вашей Key Vault. Это обеспечивает полный контроль над данными и ключами. Вы можете отключить ключи или отозвать доступ к управляемым дискам в любое время. Кроме того, можно проводить аудит использования ключа шифрования с Azure Key Vault мониторинга, чтобы обеспечить доступ к ключам только управляемых дисков или других доверенных служб Azure.

На следующей схеме показано, как управляемые диски используют Azure Active Directory и Azure Key Vault для выполнения запросов с помощью управляемого клиентом ключа:

![Рабочий процесс с управляемыми клиентами управляемыми дисками](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


В следующем списке описаны пронумерованные шаги на схеме.

1. Администратор Azure Key Vault создает ресурсы хранилища ключей.
1. Администратор хранилища ключей импортирует ключи RSA в Key Vault или создает новые ключи RSA в Key Vault.
1. Этот администратор создает экземпляр ресурса для набора шифрования диска, указывая идентификатор Azure Key Vault и URL-адрес ключа. Набор шифрования дисков — это новый ресурс, который появился для упрощения управления ключами для управляемых дисков. 
1. При создании набора шифрования дисков [назначенное системой управляемое удостоверение](../articles/active-directory/managed-identities-azure-resources/overview.md) создается в Azure Active Directory (AD) и связывается с набором шифрования дисков. 
1. Затем администратор хранилища ключей Azure предоставляет управляемому удостоверению разрешение на выполнение операций в хранилище ключей.
1. Пользователь виртуальной машины создает диски, связывая их с набором шифрования дисков. Пользователь виртуальной машины может также включить шифрование на стороне сервера с помощью управляемых клиентом ключей для существующих ресурсов, связав их с набором шифрования дисков. 
1. Управляемые диски используют управляемое удостоверение для отправки запросов в Azure Key Vault.
1. Для чтения или записи данных управляемые диски отправляют запросы в Azure Key Vault для шифрования (переноса) и расшифровки (распаковки) ключа шифрования данных для выполнения шифрования и расшифровки данных. 

Чтобы отозвать доступ к ключам, управляемым клиентом, см. статью [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) и [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Отзыв доступа блокирует доступ ко всем данным в учетной записи хранения, так как ключ шифрования недоступен для службы хранилища Azure.

### <a name="supported-scenarios-and-restrictions"></a>Поддерживаемые сценарии и ограничения

В предварительной версии поддерживаются только следующие сценарии.

- Создайте виртуальную машину из образа Azure Marketplace и зашифровать диск операционной системы с помощью шифрования на стороне сервера, используя ключи, управляемые клиентом.
- Создание пользовательского образа, зашифрованного с помощью шифрования на стороне сервера и ключей, управляемых клиентом.
- Создание виртуальной машины из пользовательского образа и шифрование диска ОС с помощью шифрования на стороне сервера и управляемых клиентом ключей.
- Создавайте диски данных, зашифрованные с помощью шифрования на стороне сервера, и ключей, управляемых клиентом.
- Создание моментальных снимков, зашифрованных с помощью шифрования на стороне сервера и управляемых клиентом ключей.
- Создание масштабируемых наборов виртуальных машин, зашифрованных с помощью шифрования на стороне сервера и ключей, управляемых клиентом.

Предварительная версия также имеет следующие ограничения.

- Доступно только в западной центральной части США.
- Диски, созданные на основе пользовательских образов, зашифрованных с помощью шифрования на стороне сервера и ключей, управляемых клиентом, должны быть зашифрованы с помощью тех же самых управляемых клиентом ключей и должны находиться в одной подписке.
- Моментальные снимки, созданные на основе дисков, зашифрованных с помощью шифрования на стороне сервера и ключей, управляемых клиентом, должны быть зашифрованы с помощью тех же управляемых клиентом ключей.
- Пользовательские образы, зашифрованные с помощью шифрования на стороне сервера и управляемых клиентом ключей, нельзя использовать в коллекции общих образов.
- Ваш Key Vault должен находиться в той же подписке и регионе, что и ключи, управляемые клиентом.
- Диски, моментальные снимки и образы, зашифрованные с помощью управляемых клиентом ключей, не могут переместиться в другую подписку.

### <a name="setting-up-your-azure-key-vault"></a>Настройка Azure Key Vault

1.  Создайте экземпляр Azure Key Vault и ключ шифрования.

    При создании экземпляра Key Vault необходимо включить защиту с обратимым удалением и очисткой. Обратимое удаление гарантирует, что Key Vault будет содержать удаленный ключ для заданного срока хранения (90 день по умолчанию). Очистка защиты гарантирует, что удаленный ключ нельзя удалить навсегда, пока не истечет срок хранения. Эти параметры защищают от потери данных из-за случайного удаления. Эти параметры являются обязательными при использовании Key Vault для шифрования управляемых дисков.

    ```powershell
    $keyVault = New-AzKeyVault -Name myKeyVaultName ` 
    -ResourceGroupName myRGName ` 
    -Location westcentralus ` 
    -EnableSoftDelete ` 
    -EnablePurgeProtection 
     
    $key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName ` 
    -Name myKeyName ` 
    -Destination Software `  
    ```

1.  Создайте экземпляр Дискенкриптионсет. 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName myRGName ` 
      -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateDiskEncryptionSet.json" ` 
      -diskEncryptionSetName "myDiskEncryptionSet1" ` 
      -keyVaultId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.KeyVault/vaults/myKeyVaultName" ` 
      -keyVaultKeyUrl "https://myKeyVaultName.vault.azure.net/keys/myKeyName/403445136dee4a57af7068cab08f7d42" ` 
      -region "WestCentralUS"
    ```

1.  Предоставьте ресурсу Дискенкриптионсет доступ к хранилищу ключей.

    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy ` 
        -VaultName $keyVault.VaultName ` 
        -ObjectId $identity.Id ` 
        -PermissionsToKeys wrapkey,unwrapkey,get 
     
    New-AzRoleAssignment ` 
        -ObjectId $identity.Id ` 
        -RoleDefinitionName "Reader" ` 
        -ResourceName $keyVault.VaultName ` 
        -ResourceType "Microsoft.KeyVault/vaults" ` 
        -ResourceGroupName myRGName `  
    ```

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys-via-a-resource-manager-template"></a>Создание виртуальной машины с помощью образа Marketplace, шифрование дисков операционной системы и данных с помощью управляемых клиентом ключей с помощью шаблона диспетчер ресурсов

```
$password=ConvertTo-SecureString -String "myVMPassword" `
  -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateVMWithDisksEncryptedWithCMK.json" `
  -virtualMachineName "myVMName" `
  -adminPassword $password `
  -vmSize "Standard_DS3_V2" `
  -diskEncryptionSetId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.Compute/diskEncryptionSets/myDiskEncryptionSet1" `
  -region "westcentralus" 
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Создайте пустой диск, зашифрованный с помощью шифрования на стороне сервера, с ключами, управляемыми клиентом, и подключите его к виртуальной машине.

```PowerShell
$vmName = "yourVMName"
$rgName = "yourRGName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = "30"
$diskEncryptionSetId = "/subscriptions/<subscriptionID>/resourceGroups/yourRGName/providers/Microsoft.Compute/diskEncryptionSets/<yourDiskEncryptionSetName>"
$region = "westcentralus"
$diskLUN = 1

New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateEmptyDataDiskEncryptedWithSSECMK.json" `
  -diskName $diskName `
  -diskSkuName $diskSKU `
  -dataDiskSizeInGb $diskSizeinGiB `
  -diskEncryptionSetId $diskEncryptionSetId `
  -region $region 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun 1
```


> [!IMPORTANT]
> Управляемые клиентом ключи используют управляемые удостоверения для ресурсов Azure, функции Azure Active Directory (Azure AD). При настройке управляемых пользователем ключей управляемое удостоверение автоматически назначается вашим ресурсам. При последующем перемещении подписки, группы ресурсов или управляемого диска из одного каталога Azure AD в другой управляемое удостоверение, связанное с управляемыми дисками, не передается в новый клиент, поэтому управляемые клиентом ключи могут перестать работать. Дополнительные сведения см. [в статье передача подписки между каталогами Azure AD](../articles/active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Шифрование на стороне сервера и шифрование дисков Azure

[Шифрование дисков Azure](../articles/security/fundamentals/azure-disk-encryption-vms-vmss.md) использует функцию [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) в Windows и функцию [DM-](https://en.wikipedia.org/wiki/Dm-crypt) Encryption в Linux для шифрования управляемых дисков с помощью управляемых клиентом ключей на гостевой виртуальной машине.  Шифрование на стороне сервера с помощью управляемых клиентом ключей улучшает работу с ADE, позволяя использовать любые типы ОС и образы для виртуальных машин путем шифрования данных в службе хранилища.

## <a name="next-steps"></a>Дальнейшие действия

- [Что такое хранилище ключей Azure?](../articles/key-vault/key-vault-overview.md)
