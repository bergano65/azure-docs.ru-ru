---
title: Восстановление ключа и секрета в хранилище ключей для зашифрованных виртуальных машин с помощью службы архивации Azure
description: Узнайте, как восстановить ключ и секрет хранилища ключей в службе архивации Azure с помощью PowerShell
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: geg
ms.openlocfilehash: 63c492f502634c6c60a6f9ec0f0d7003e56ac0a2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705023"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Восстановление ключа и секрета в хранилище ключей для зашифрованных виртуальных машин с помощью службы архивации Azure

В этой статье рассказывается об использовании службы архивации виртуальных машин Azure для восстановления зашифрованных виртуальных машин Azure, когда ключ и секрет отсутствуют в хранилище ключей. Эта процедура будет также полезной, если нужно хранить отдельную копию ключа (ключ шифрования ключа) и секрета (ключ шифрования BitLocker) для восстанавливаемой виртуальной машины.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>предварительные требования

* **Резервная копия зашифрованных виртуальных машин** — выполнять резервное копирование зашифрованных виртуальных машин Azure нужно с помощью службы архивации Azure. Резервное копирование зашифрованных виртуальных машин Azure см. в статье [Manage backup and restore of Azure VMs using PowerShell](backup-azure-vms-automation.md) (Управление резервным копированием и восстановлением виртуальных машин Azure с помощью PowerShell).
* **Настроенное хранилище ключей Azure** — убедитесь, что хранилище ключей, в которое нужно восстановить ключи и секреты, существует. Управление хранилищами ключей описано в статье [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md).
* **Восстановление диска** — убедитесь, что запущено задание восстановления дисков для зашифрованной виртуальной машины с помощью [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Это связано с тем, что данное задание создает JSON-файл в учетной записи хранения, которая содержит ключи и секреты для восстанавливаемой зашифрованной виртуальной машины.

## <a name="get-key-and-secret-from-azure-backup"></a>Получение ключа и секрета из службы Azure Backup

> [!NOTE]
> После восстановления диска для зашифрованной виртуальной машины убедитесь, что выполняются следующие требования:
> * Переменная $details заполняется сведениями о задании восстановления диска, как описано в [разделе о восстановлении дисков с помощью PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).
> * Создавать виртуальную машину из восстановленных дисков следует только **после восстановления ключа и секрета в хранилище ключей**.

Запросите свойства восстановленного диска для получения сведений о задании.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Задайте контекст хранилища Azure и восстановите файл конфигурации JSON, который содержит ключ и секрет для зашифрованной виртуальной машины.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Ключ восстановления

Когда JSON-файл создан в упомянутом выше конечном пути, создайте из этого JSON-файла ключ в виде файла большого двоичного объекта и укажите его при выполнении командлета восстановления ключа, чтобы поместить ключ (KEK) обратно в хранилище ключей.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Восстановление секрета

Используйте созданный выше JSON-файл для получения имени и значения секрета и укажите его при выполнении командлета настройки секрета, чтобы поместить секрет (BEK) обратно в хранилище ключей. **Воспользуйтесь этими командлетами, если виртуальная машина зашифрована с помощью BEK и KEK.**

**Воспользуйтесь этими командлетами, если виртуальная машина Windows зашифрована с помощью BEK и KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Воспользуйтесь этими командлетами, если виртуальная машина Linux зашифрована с помощью BEK и KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Используйте созданный выше JSON-файл для получения имени и значения секрета и укажите его при выполнении командлета настройки секрета, чтобы поместить секрет (BEK) обратно в хранилище ключей. **Воспользуйтесь этими командлетами, если виртуальная машина зашифрована только с помощью BEK.**

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
> * Значение переменной $secretname можно получить из выходных данных параметра $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl, используя текст после secrets/. Например, выходной URL-адрес секрета — https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163, а имя секрета — B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.
> * Значение тега DiskEncryptionKeyFileName совпадает с именем секрета.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Создание виртуальной машины на основе восстановленного диска

Если ранее была создана резервная копия зашифрованной виртуальной машины с помощью службы архивации виртуальных машин Azure, то указанные выше командлеты PowerShell позволяют восстановить ключ и секрет в хранилище ключей. После их восстановления см. статью [Архивация виртуальных машин с помощью командлетов AzureRM.RecoveryServices.Backup](backup-azure-vms-automation.md#create-a-vm-from-restored-disks), в которой описано, как создать зашифрованные виртуальные машины, используя восстановленный диск, ключ и секрет.

## <a name="legacy-approach"></a>Устаревший подход

Описанный выше подход подойдет для всех точек восстановления. Однако более старый подход получения сведений о ключе и секрете из точки восстановления можно по-прежнему использовать для точек восстановления, созданных до 11 июля 2017 г. (для виртуальных машин, зашифрованных с помощью BEK и KEK). После выполнения задания восстановления диска для зашифрованной виртуальной машины с помощью [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) убедитесь, что переменная $rp заполняется допустимым значением.

### <a name="restore-key"></a>Ключ восстановления

Используйте следующие командлеты для получения из точки восстановления сведений о ключе (KEK) и укажите его при выполнении командлета восстановления ключа, чтобы поместить его обратно в хранилище ключей.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Восстановление секрета

Используйте следующие командлеты для получения из точки восстановления сведений о секрете (BEK) и укажите его при выполнении командлета настройки секрета, чтобы поместить его обратно в хранилище ключей.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> * Значение переменной $secretname можно получить из выходных данных параметра $rp1.KeyAndSecretDetails.SecretUrl, используя текст после secrets/. Например, выходной URL-адрес секрета — https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163, а имя секрета — B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.
> * Значение тега DiskEncryptionKeyFileName совпадает с именем секрета.
> * Значение для DiskEncryptionKeyEncryptionKeyURL можно получить из хранилища ключей после восстановления ключей с помощью командлета [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey).
>
>

## <a name="next-steps"></a>Следующие шаги

После восстановления ключа и секрета в хранилище ключей см. статью [Архивация виртуальных машин с помощью командлетов AzureRM.RecoveryServices.Backup](backup-azure-vms-automation.md#create-a-vm-from-restored-disks), в которой описано, как создать зашифрованные виртуальные машины, используя восстановленный диск, ключ и секрет.
