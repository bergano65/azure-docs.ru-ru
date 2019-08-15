---
title: Настройка ключей, управляемых клиентом, для шифрования службы хранилища Azure из PowerShell
description: Узнайте, как использовать PowerShell для настройки ключей, управляемых клиентом, для шифрования службы хранилища Azure. Ключи, управляемые клиентом, позволяют создавать, поворачивать, отключать и отзывать элементы управления доступом.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6c9adf1c00503ec7f1cbf4a3405c303eea2d2292
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034880"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Настройка ключей, управляемых клиентом, для шифрования службы хранилища Azure из PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

В этой статье показано, как настроить хранилище ключей с управляемыми клиентом ключами с помощью PowerShell.

> [!IMPORTANT]
> Для использования управляемых клиентом ключей с шифрованием службы хранилища Azure необходимо, чтобы в хранилище ключей было настроено два обязательных свойства, **обратимое удаление** и **не выполнять очистку**. Эти свойства включены по умолчанию при создании нового хранилища ключей в портал Azure. Однако если необходимо включить эти свойства в существующем хранилище ключей, необходимо использовать PowerShell или Azure CLI.
> Поддерживаются только ключи RSA и размер ключа 2048.

## <a name="assign-an-identity-to-the-storage-account"></a>Назначение удостоверения учетной записи хранения

Чтобы включить управляемые клиентом ключи для учетной записи хранения, сначала назначьте назначенное системой управляемое удостоверение учетной записи хранения. Вы будете использовать это управляемое удостоверение, чтобы предоставить учетной записи хранения разрешения на доступ к хранилищу ключей.

Чтобы назначить управляемое удостоверение с помощью PowerShell, вызовите [Set-азсторажеаккаунт](/powershell/module/az.storage/set-azstorageaccount). Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Дополнительные сведения о настройке управляемых удостоверений, назначенных системой с помощью PowerShell, см. [в статье Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Создание нового хранилища ключей

Чтобы создать новое хранилище ключей с помощью PowerShell, вызовите [New-азкэйваулт](/powershell/module/az.keyvault/new-azkeyvault). Хранилище ключей, используемое для хранения управляемых клиентом ключей для шифрования службы хранилища Azure, должно включать два параметра защиты ключей: **обратимое удаление** и **не очищать**. 

Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Настройка политики доступа к хранилищу ключей

Затем настройте политику доступа для хранилища ключей, чтобы у учетной записи хранения были разрешения на доступ к ней. На этом шаге вы будете использовать управляемое удостоверение, назначенное ранее учетной записи хранения.

Чтобы задать политику доступа для хранилища ключей, вызовите [Set-азкэйваултакцессполици](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями и использовать переменные, определенные в предыдущих примерах.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Создайте ключ

Затем создайте новый ключ в хранилище ключей. Чтобы создать новый ключ, вызовите [Add-азкэйваулткэй](/powershell/module/az.keyvault/add-azkeyvaultkey). Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями и использовать переменные, определенные в предыдущих примерах.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Настройка шифрования с помощью управляемых клиентом ключей

По умолчанию шифрование службы хранилища Azure использует ключи, управляемые корпорацией Майкрософт. На этом шаге настройте учетную запись хранения Azure для использования ключей, управляемых клиентом, и укажите ключ, связываемый с учетной записью хранения.

Вызовите [Set-азсторажеаккаунт](/powershell/module/az.storage/set-azstorageaccount) , чтобы обновить параметры шифрования учетной записи хранения. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями и использовать переменные, определенные в предыдущих примерах.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Обновление версии ключа

При создании новой версии ключа необходимо обновить учетную запись хранения, чтобы она использовала новую версию. Сначала вызовите [Get-азкэйваулткэй](/powershell/module/az.keyvault/get-azkeyvaultkey) , чтобы получить последнюю версию ключа. Затем вызовите [Set-азсторажеаккаунт](/powershell/module/az.storage/set-azstorageaccount) , чтобы обновить параметры шифрования учетной записи хранения для использования новой версии ключа, как показано в предыдущем разделе.

## <a name="next-steps"></a>Следующие шаги

- [Шифрование неактивных данных в службе хранилища Azure](storage-service-encryption.md) 
- [Что такое Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
