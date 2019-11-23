---
title: Remove TDE protector - PowerShell
description: Практическое руководство по реагированию на потенциальную компрометацию предохранителя TDE для базы данных SQL Azure или хранилища данных, для которых настроено прозрачное шифрование данных (TDE) с поддержкой создания собственных ключей (BYOK).
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
ms.openlocfilehash: 29971414219976f6d72caf30a909f1884b04aef7
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422423"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Удаление предохранителя TDE с помощью PowerShell

## <a name="prerequisites"></a>Технические условия

- Подписка Azure и права администратора для нее.
- You must have Azure PowerShell installed and running.
- В этом практическом руководстве предполагается, что вы уже используете ключ из Azure Key Vault в качестве предохранителя TDE для базы данных SQL Azure или хранилища данных. См. дополнительные сведения о [прозрачном шифровании данных с поддержкой создания собственных ключей](transparent-data-encryption-byok-azure-sql.md).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 Инструкции по установке модуля Az см. в статье об [установке Azure PowerShell](/powershell/azure/install-az-ps). For specific cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

For installation, see [Install Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Краткое описание

Это практическое руководство описывает, как правильно реагировать на потенциальную компрометацию предохранителя TDE для базы данных SQL Azure или хранилища данных SQL, для которых настроено TDE с ключами, управляемыми клиентом, в Azure Key Vault с поддержкой BYOK. См. дополнительные сведения о [поддержке BYOK для TDE](transparent-data-encryption-byok-azure-sql.md).

Следующие процедуры следует выполнять только в крайних случаях или тестовых средах. Внимательно изучите это практическое руководство, так как удаление используемых предохранителей TDE из Azure Key Vault может привести к **потере данных**.

Если возникнут любые подозрения по поводу компрометации ключа, например путем несанкционированного доступа пользователей и (или) служб к этому ключу, его следует удалить.

Но при этом не забывайте, что сразу после удаления предохранителя TDE в Key Vault **блокируются все подключения к зашифрованным базам данных на сервере, и эти базы данных переходят в автономный режим, а затем удаляются в течение 24 часов**. Старые резервные копии, зашифрованные с помощью скомпрометированного ключа, становятся недоступными.

The following steps outline how to check the TDE Protector thumbprints still in use by Virtual Log Files (VLF) of a given database.
The thumbprint of the current TDE protector of the database, and the database ID can be found by running:

```sql
SELECT [database_id], 
       [encryption_state], 
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/ 
       [encryptor_thumbprint], 
 FROM [sys].[dm_database_encryption_keys]
```

The following query returns the VLFs and the encryptor respective thumbprints in use. Each different thumbprint refers to different key in Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

The PowerShell command **Get-AzureRmSqlServerKeyVaultKey** provides the thumbprint of the TDE Protector used in the query, so you can see which keys to keep and which keys to delete in AKV. Only keys no longer used by the database can be safely deleted from Azure Key Vault.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

The PowerShell command **az sql server key show** provides the thumbprint of the TDE Protector used in the query, so you can see which keys to keep and which keys to delete in AKV. Only keys no longer used by the database can be safely deleted from Azure Key Vault.

* * *

В этом практическом руководстве рассматриваются два подхода в зависимости от результата, которого вы хотите добиться с помощью реагирования:

- вы можете **сохранить доступность** баз данных SQL Azure и (или) хранилищ данных;
- вы можете **заблокировать доступность** баз данных SQL Azure и (или) хранилищ данных.

## <a name="to-keep-the-encrypted-resources-accessible"></a>Сохранение доступности зашифрованных ресурсов

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Создайте [ключ в Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Убедитесь, что новый ключ успешно создан в другом хранилище. Нельзя использовать то же хранилище, где размещен скомпрометированный предохранитель TDE, так как управление доступом предоставляется на уровне хранилища.

2. Add the new key to the server using the [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) and [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets and update it as the server’s new TDE protector.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Make sure the server and any replicas have updated to the new TDE protector using the [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet.

   > [!NOTE]
   > Распространение нового предохранителя TDE на все базы данных-источники и базы данных-получатели для обновляемого сервера может занять несколько минут.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Создайте [резервную копию нового ключа](/powershell/module/az.keyvault/backup-azkeyvaultkey) в Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Delete the compromised key from Key Vault using the [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet.

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. To restore a key to Key Vault in the future using the [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet:

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

For command reference, see the [Azure CLI keyvault](/cli/azure/keyvault/key).

1. Создайте [ключ в Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Убедитесь, что новый ключ успешно создан в другом хранилище. Нельзя использовать то же хранилище, где размещен скомпрометированный предохранитель TDE, так как управление доступом предоставляется на уровне хранилища.

2. Add the new key to the server and update it as the server’s new TDE protector.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Make sure the server and any replicas have updated to the new TDE protector.

   > [!NOTE]
   > Распространение нового предохранителя TDE на все базы данных-источники и базы данных-получатели для обновляемого сервера может занять несколько минут.

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Take a backup of the new key in Key Vault.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Delete the compromised key from Key Vault.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. To restore a key to Key Vault in the future.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Блокирование доступности зашифрованных ресурсов

1. Удалите все базы данных, которые зашифрованы с помощью потенциально скомпрометированного ключа.

   Для файлов базы данных и журналов резервные копии создаются автоматически, поэтому вы всегда сможете восстановить базу данных на определенный момент времени, предоставив нужный ключ. Удаление баз данных перед удалением действующего предохранителя TDE нужно для того, чтобы предотвратить потерю данных из последних транзакций (до 10 минут).

2. Создайте в Key Vault резервную копию материала ключа для предохранителя TDE.
3. Удалите потенциально скомпрометированный ключ из Key Vault.

## <a name="next-steps"></a>Дальнейшие действия

- См. дополнительные сведения о том, как [заменить предохранитель TDE для сервера в соответствии с требованиями к безопасности с помощью PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- См. дополнительные сведения о [включении прозрачного шифрования данных с помощью собственного ключа из Key Vault с помощью PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
