---
title: Используйте PowerShell для настройки ключей, управляемых клиентом
titleSuffix: Azure Storage
description: Узнайте, как использовать PowerShell для настройки управляемых клиентом ключей для шифрования Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfc2e256396904456a7ee0fd8b6173c00a5f53d7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456402"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Налаживание ключей, управляемых клиентами, с помощью Хранилища ключей Azure с помощью PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

В этой статье показано, как настроить Хранилище ключей Azure с ключами, управляемыми клиентами, с помощью PowerShell. Чтобы узнать, как создать хранилище ключей с помощью Azure CLI, [см.](../../key-vault/secrets/quick-create-powershell.md)

## <a name="assign-an-identity-to-the-storage-account"></a>Присвоить удостоверение личности учетной записи хранилища

Чтобы включить управляемые клиентом ключи для учетной записи хранилища, сначала назначьте учетную запись, назначенную системой, управляемую учетную запись. Это управляемое удостоверение будет использоваться для предоставления разрешения учетной записи хранилища на доступ к хранилищу ключей.

Чтобы назначить управляемую идентификацию с помощью PowerShell, позвоните [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Не забудьте заменить значения заполнителя в скобках с вашими собственными значениями.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Для получения дополнительной информации о настройке назначенных системами управляемых идентификаторов с PowerShell см. [Наверстные управляемые идентификаторы для ресурсов Azure на Azure VM с помощью PowerShell.](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)

## <a name="create-a-new-key-vault"></a>Создание нового хранилища ключей

Чтобы создать новый хранилище ключей с помощью PowerShell, [позвоните в New-AzKeyVault.](/powershell/module/az.keyvault/new-azkeyvault) В хранилище ключей, которое используется для хранения управляемых клиентом ключей для шифрования Azure Storage, должно быть включено два ключевых настройки защиты: **Soft Delete** и Do **Not Purge.**

Не забудьте заменить значения заполнителя в скобках с вашими собственными значениями.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Чтобы узнать, как включить Soft **Delete** и **Не очищайте** на **Enabling soft-delete** существующем хранилище ключей с [How to use soft-delete with PowerShell](../../key-vault/general/soft-delete-powershell.md)PowerShell, см. **Enabling Purge Protection**

## <a name="configure-the-key-vault-access-policy"></a>Настройка политики доступа к хранилищу ключей

Затем наверсните политику доступа для хранилища ключей таким образом, чтобы учетная запись хранилища получила разрешение на доступ к нему. На этом этапе вы будете использовать управляемую идентификацию, которую вы ранее назначили учетной записи хранилища.

Чтобы установить политику доступа для хранилища ключей, позвоните [set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Не забудьте заменить значения заполнителя в скобках на собственные значения и использовать переменные, определенные в предыдущих примерах.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Создание ключа

Затем создайте новый ключ в хранилище ключей. Чтобы создать новый ключ, позвоните [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Не забудьте заменить значения заполнителя в скобках на собственные значения и использовать переменные, определенные в предыдущих примерах.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Только 2048-разрядные клавиши RSA и RSA-HSM поддерживаются шифрованием Azure Storage. Для получения дополнительной информации о ключах смотрите **ключи Убежища ключей** в [о клавишах, секретах и сертификатах Azure Key Vault.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)

## <a name="configure-encryption-with-customer-managed-keys"></a>Настройка шифрования с помощью ключей, управляемых клиентом

По умолчанию шифрование Azure Storage использует ключи, управляемые корпорацией Майкрософт. На этом этапе наймите учетную запись Azure Storage, чтобы использовать управляемые клиентом ключи и укажите ключ, который можно связать с учетной записью хранилища.

Позвоните [Set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) чтобы обновить настройки шифрования учетной записи хранилища, как показано в следующем примере. Включите опцию **-KeyvaultEncryption,** чтобы включить управляемые клиентом ключи для учетной записи хранения. Не забудьте заменить значения заполнителя в скобках на собственные значения и использовать переменные, определенные в предыдущих примерах.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Обновление ключевой версии

При создании новой версии ключа необходимо обновить учетную запись хранилища, чтобы использовать новую версию. Во-первых, позвоните [Get-AzKeyVaultKey,](/powershell/module/az.keyvault/get-azkeyvaultkey) чтобы получить последнюю версию ключа. Затем [позвоните set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) чтобы обновить настройки шифрования учетной записи хранилища, чтобы использовать новую версию ключа, как показано в предыдущем разделе.

## <a name="use-a-different-key"></a>Используйте другой ключ

Чтобы изменить ключ, используемый для шифрования хранилища Azure, позвоните [Set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) как показано в [шифровании Configure с ключами, управляемыми клиентом,](#configure-encryption-with-customer-managed-keys) и предоставьте новое ключевое имя и версию. Если новый ключ находится в другом хранилище ключей, также обновите хранилище ключей URI.

## <a name="revoke-customer-managed-keys"></a>Отозвать ключи, управляемые клиентами

Если вы считаете, что ключ может быть скомпрометирован, вы можете отозвать ключи, управляемые клиентом, удалив политику доступа к хранилищу ключей. Чтобы отозвать ключ, управляемый клиентом, позвоните в команду [Remove-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) как показано в следующем примере. Не забудьте заменить значения заполнителя в скобках на собственные значения и использовать переменные, определенные в предыдущих примерах.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Отключить ключи, управляемые клиентом

При отключении ключей, управляемых клиентом, ваша учетная запись хранилища снова шифруется ключами, управляемыми корпорацией Майкрософт. Чтобы отключить клавиши, управляемые клиентом, позвоните `-StorageEncryption` [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) с опцией, как показано в следующем примере. Не забудьте заменить значения заполнителя в скобках на собственные значения и использовать переменные, определенные в предыдущих примерах.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Следующие шаги

- [Шифрование хранилища Azure для данных в состоянии покоя](storage-service-encryption.md)
- [Что такое Убежище ключей Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
