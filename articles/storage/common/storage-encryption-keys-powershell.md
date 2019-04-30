---
title: Настройка управляемых пользователем ключей для шифрования службы хранилища Azure с помощью PowerShell
description: Узнайте, как использовать PowerShell для настройки управляемых пользователем ключей для шифрования службы хранилища Azure. Управляемые клиентом ключи позволяют создавать, поворот, отключить и отозвать элементы управления доступом.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 01d5226a2d6f4c4e2e376803a3f0a25d2d5b0fd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593567"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Настройка управляемых пользователем ключей для шифрования службы хранилища Azure с помощью PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

В этой статье показано, как настроить хранилище ключей с помощью управляемых клиентом ключей с помощью PowerShell.

## <a name="assign-an-identity-to-the-storage-account"></a>Назначение удостоверения учетной записи хранения

Чтобы включить управляемые клиентом ключи для вашей учетной записи хранения, сначала назначьте назначенный системой управляемого удостоверения учетной записи хранения. Вы используете это управляемое удостоверение для предоставления разрешений учетной записи хранения для доступа к хранилищу ключей.

Чтобы назначить управляемого удостоверения с помощью PowerShell, вызовите [AzStorageAccount набора](/powershell/module/az.storage/set-azstorageaccount). Не забудьте заменить значения заполнителей в квадратные скобки, своими собственными значениями.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Дополнительные сведения о настройке управляемого удостоверения системы с помощью PowerShell см. в разделе [Настройка управляемых удостоверений для ресурсов Azure на виртуальной Машине Azure с помощью PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Создание нового хранилища ключей

Чтобы создать новое хранилище ключей с помощью PowerShell, вызовите [New AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Хранилище ключей, которое используется для хранения управляемых пользователем ключей для шифрования службы хранилища Azure должна иметь два параметра ключа защиты включен, **обратимое удаление** и **не следует очистить**. 

Не забудьте заменить значения заполнителей в квадратные скобки, своими собственными значениями. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Настройка политики доступа хранилища ключей

Настройте политики доступа для хранилища ключей, чтобы учетная запись хранения имеет разрешения на доступ к нему. На этом шаге вы используете управляемое удостоверение, которое ранее было назначено учетной записи хранения.

Чтобы задать политику доступа для хранилища ключей, вызовите [AzKeyVaultAccessPolicy набора](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Не забудьте заменить значения заполнителей в квадратные скобки, своими собственными значениями и использовать переменные, определенные в предыдущих примерах.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Создайте ключ

Создайте новый ключ в хранилище ключей. Чтобы создать новый ключ, вызовите [AzKeyVaultKey добавить](/powershell/module/az.keyvault/add-azkeyvaultkey). Не забудьте заменить значения заполнителей в квадратные скобки, своими собственными значениями и использовать переменные, определенные в предыдущих примерах.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Настройка шифрования с помощью управляемых клиентом ключей

По умолчанию шифрование службы хранилища Azure использует ключи, управляемые Майкрософт. На этом этапе Настройка вашей учетной записи хранения Azure, чтобы использовать управляемые клиентом ключи и указать ключ для связи с учетной записью хранения.

Вызовите [AzStorageAccount набора](/powershell/module/az.keyvault/set-azstorageaccount) обновить параметры шифрования для учетной записи хранения. Не забудьте заменить значения заполнителей в квадратные скобки, своими собственными значениями и использовать переменные, определенные в предыдущих примерах.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Обновление версии ключа

При создании новой версии ключа, вам потребуется обновить учетную запись хранения для использования новой версии. Во-первых, вызовите [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) для получения последней версии ключа. Затем вызовите [AzStorageAccount набора](/powershell/module/az.keyvault/set-azstorageaccount) для обновления параметров шифрования учетной записи хранения для использования новой версии ключа, как показано в предыдущем разделе.

## <a name="next-steps"></a>Дальнейшие действия

- [Шифрование хранилища Azure для неактивных данных](storage-service-encryption.md) 
- [Что такое хранилище ключей Azure](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
