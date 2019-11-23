---
title: Enable SQL TDE with Azure Key Vault
description: Сведения о настройке прозрачного шифрования данных в базе данных SQL Azure и хранилище данных с помощью PowerShell или интерфейса командной строки для шифрования данных при хранении.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: e60b83f8eb1e95733512180417bb2a1be679b0a0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422464"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell and CLI: Enable Transparent Data Encryption with customer-managed key from Azure Key Vault

В этой статье рассматриваются способы применения ключа из Azure Key Vault для прозрачного шифрования данных (TDE) в Базе данных SQL или хранилище данных. Чтобы получить дополнительные сведения об интеграции Azure Key Vault и поддержке при создании собственных ключей для TDE, см. статью [Прозрачное шифрование данных в Azure SQL. Поддержка создания собственных ключей](transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites-for-powershell"></a>Предварительные требования для PowerShell

- Подписка Azure и права администратора для нее.
- [Рекомендуется, но необязательно.] Аппаратный модуль безопасности (HSM) или локальное хранилище ключей для создания локальной копии материала ключа для предохранителя TDE.
- You must have Azure PowerShell installed and running.
- Создайте Azure Key Vault и ключи для TDE.
  - [Инструкции по использованию аппаратного модуля безопасности (HSM) и Key Vault](../key-vault/key-vault-hsm-protected-keys.md).
    - Чтобы использовать хранилище ключей для TDE, обязательно настройте для него следующее свойство:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md) and purge protection
- Чтобы использовать ключ для TDE, обязательно настройте для него следующие атрибуты:
   - без даты окончания срока действия;
   - не отключено;
   - возможность выполнять операции *получения*, *упаковки ключа* и *распаковки ключа*.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Инструкции по установке модуля Az см. в статье об [установке Azure PowerShell](/powershell/azure/install-az-ps). For specific cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

For specifics on Key Vault, see [PowerShell instructions from Key Vault](../key-vault/quick-create-powershell.md) and [How to use Key Vault soft-delete with PowerShell](../key-vault/key-vault-soft-delete-powershell.md).

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Назначение серверу удостоверения Azure AD

Если у вас есть существующий сервер, выполните следующие действия для присвоения этому серверу удостоверения Azure AD:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

If you are creating a server, use the [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet with the tag -Identity to add an Azure AD identity during server creation:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Предоставление серверу разрешений на доступ к Key Vault

Use the [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet to grant your server access to the key vault before using a key from it for TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Добавление на сервер ключа из Key Vault и настройка предохранителя TDE

- Use the [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) cmdlet to retrieve the key ID from key vault
- Use the [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet to add the key from the Key Vault to the server.
- Use the [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet to set the key as the TDE protector for all server resources.
- Use the [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet to confirm that the TDE protector was configured as intended.

> [!NOTE]
> Общая длина имени хранилища ключей и имени ключа не может превышать 94 символа.

> [!TIP]
> Пример идентификатора KeyId из Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Включение прозрачного шифрования данных

Use the [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet to turn on TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Теперь для базы данных или хранилища данных включено прозрачное шифрование данных с ключом шифрования из Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Проверка состояния и активности шифрования

Use the [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) to get the encryption state and the [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) to check the encryption progress for a database or data warehouse.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

To install the required Command-Line Interface version 2.0 or later and connect to your Azure subscription, see [Install and Configure the Azure Cross-Platform Command-Line Interface 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

For specifics on Key Vault, see [Manage Key Vault using CLI 2.0](../key-vault/key-vault-manage-with-cli2.md) and [How to use Key Vault soft-delete with CLI](../key-vault/key-vault-soft-delete-cli.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Назначение серверу удостоверения Azure AD

```powershell
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Нужно, чтобы "principalID" не создавал сервер. На следующем шаге для назначения разрешений хранилища ключей используется идентификатор объекта.

## <a name="grant-key-vault-permissions-to-your-server"></a>Предоставление серверу разрешений на доступ к Key Vault

```powershell
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Для следующего шага сохраните URI или идентификатор нового ключа, например https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h.

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Добавление на сервер ключа из Key Vault и настройка предохранителя TDE

```powershell
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> Общая длина имени хранилища ключей и имени ключа не может превышать 94 символа.

## <a name="turn-on-tde"></a>Включение прозрачного шифрования данных

```powershell
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Теперь в базе данных или хранилище данных существует включенный TDE с ключом шифрования, управляемым клиентом, в Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Проверка состояния и активности шифрования

```powershell
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Полезные командлеты PowerShell

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- Use the [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet to turn off TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled”
   ```

- Use the [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet to return the list of Key Vault keys added to the server.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Use the [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) to remove a Key Vault key from the server.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

- For general database settings, see [az sql](/cli/azure/sql).

- For vault key settings, see [az sql server key](/cli/azure/sql/server/key).

- For TDE settings, see [az sql server tde-key](/cli/azure/sql/server/tde-key) and [az sql db tde](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Устранение неисправностей

При возникновении ошибок выполните следующие проверки.

- If the key vault cannot be found, make sure you're in the right subscription.

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Если не удается добавить на сервер новый ключ или указать новый в качестве предохранителя TDE, проверьте следующее:
   - ключ не должен иметь дату окончания срока действия;
   - для ключа должны быть включены операции *получения*, *упаковки ключа* и *распаковки ключа*.

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о том, как заменить предохранитель TDE для сервера в соответствии с требованиями безопасности, приводятся в [этой статье](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Сведения о том, как удалить потенциально скомпрометированный предохранитель TDE в случае угрозы безопасности, вы найдете в [этой статье](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).
