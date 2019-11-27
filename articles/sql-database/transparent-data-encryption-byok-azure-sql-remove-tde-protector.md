---
title: Удаление предохранителя TDE с PowerShell
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

## <a name="prerequisites"></a>предварительным требованиям

- Подписка Azure и права администратора для нее.
- Необходимо установить и запустить Azure PowerShell.
- В этом практическом руководстве предполагается, что вы уже используете ключ из Azure Key Vault в качестве предохранителя TDE для базы данных SQL Azure или хранилища данных. См. дополнительные сведения о [прозрачном шифровании данных с поддержкой создания собственных ключей](transparent-data-encryption-byok-azure-sql.md).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 Инструкции по установке модуля Az см. в статье об [установке Azure PowerShell](/powershell/azure/install-az-ps). Конкретные командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager (RM) по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Модуль AzureRM продолжит принимать исправления ошибок до 2020 декабря.  Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны. Дополнительные сведения о совместимости см. [в разделе Введение в новый модуль Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Сведения об установке см. в разделе [Install Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Обзор

Это практическое руководство описывает, как правильно реагировать на потенциальную компрометацию предохранителя TDE для базы данных SQL Azure или хранилища данных SQL, для которых настроено TDE с ключами, управляемыми клиентом, в Azure Key Vault с поддержкой BYOK. См. дополнительные сведения о [поддержке BYOK для TDE](transparent-data-encryption-byok-azure-sql.md).

Следующие процедуры следует выполнять только в крайних случаях или тестовых средах. Внимательно изучите это практическое руководство, так как удаление используемых предохранителей TDE из Azure Key Vault может привести к **потере данных**.

Если возникнут любые подозрения по поводу компрометации ключа, например путем несанкционированного доступа пользователей и (или) служб к этому ключу, его следует удалить.

Но при этом не забывайте, что сразу после удаления предохранителя TDE в Key Vault **блокируются все подключения к зашифрованным базам данных на сервере, и эти базы данных переходят в автономный режим, а затем удаляются в течение 24 часов**. Старые резервные копии, зашифрованные с помощью скомпрометированного ключа, становятся недоступными.

В следующих шагах показано, как проверить, какие отпечатки предохранителя TDE все еще используются виртуальными файлами журнала (VLF) заданной базы данных.
Отпечаток текущего предохранителя TDE базы данных и идентификатор базы данных можно найти, выполнив команду:

```sql
SELECT [database_id], 
       [encryption_state], 
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/ 
       [encryptor_thumbprint], 
 FROM [sys].[dm_database_encryption_keys]
```

Следующий запрос возвращает VLF и используемый им отпечаток. Каждый отпечаток относится к разным ключам в Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Команда PowerShell **Get-AzureRmSqlServerKeyVaultKey** предоставляет отпечаток предохранителя TDE, используемого в запросе, чтобы можно было видеть, какие ключи следует удерживать, а какие — удалять в AKV. Из Azure Key Vault можно безопасно удалить только те ключи, которые больше не используются базой данных.

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Команда PowerShell **AZ SQL Server Key показать** предоставляет отпечаток предохранителя TDE, используемого в запросе, чтобы можно было увидеть, какие ключи следует удерживать и какие ключи нужно удалить в AKV. Из Azure Key Vault можно безопасно удалить только те ключи, которые больше не используются базой данных.

* * *

В этом практическом руководстве рассматриваются два подхода в зависимости от результата, которого вы хотите добиться с помощью реагирования:

- вы можете **сохранить доступность** баз данных SQL Azure и (или) хранилищ данных;
- вы можете **заблокировать доступность** баз данных SQL Azure и (или) хранилищ данных.

## <a name="to-keep-the-encrypted-resources-accessible"></a>Сохранение доступности зашифрованных ресурсов

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Создайте [ключ в Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Убедитесь, что новый ключ успешно создан в другом хранилище. Нельзя использовать то же хранилище, где размещен скомпрометированный предохранитель TDE, так как управление доступом предоставляется на уровне хранилища.

2. Добавьте новый ключ на сервер с помощью командлетов [Add-азсклсерверкэйваулткэй](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) и [Set-азсклсервертранспарентдатаенкриптионпротектор](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) и обновите его в качестве нового предохранителя TDE для сервера.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Убедитесь, что сервер и все реплики обновлены до нового предохранителя TDE с помощью командлета [Get-азсклсервертранспарентдатаенкриптионпротектор](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) .

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

5. Удалите скомпрометированный ключ из Key Vault с помощью командлета [Remove-азкэйваулткэй](/powershell/module/az.keyvault/remove-azkeyvaultkey) .

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Чтобы восстановить ключ для Key Vault в будущем с помощью командлета [RESTORE-азкэйваулткэй](/powershell/module/az.keyvault/restore-azkeyvaultkey) :

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Справочник по командам см. в [Azure CLI keyvault](/cli/azure/keyvault/key).

1. Создайте [ключ в Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Убедитесь, что новый ключ успешно создан в другом хранилище. Нельзя использовать то же хранилище, где размещен скомпрометированный предохранитель TDE, так как управление доступом предоставляется на уровне хранилища.

2. Добавьте новый ключ на сервер и обновите его как новый компонент защиты TDE сервера.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Убедитесь, что сервер и все реплики обновлены до нового предохранителя TDE.

   > [!NOTE]
   > Распространение нового предохранителя TDE на все базы данных-источники и базы данных-получатели для обновляемого сервера может занять несколько минут.

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Создайте резервную копию нового ключа в Key Vault.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Удалите скомпрометированный ключ из Key Vault.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Восстановление ключа для Key Vault в будущем.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Блокирование доступности зашифрованных ресурсов

1. Удалите все базы данных, которые зашифрованы с помощью потенциально скомпрометированного ключа.

   Для файлов базы данных и журналов резервные копии создаются автоматически, поэтому вы всегда сможете восстановить базу данных на определенный момент времени, предоставив нужный ключ. Удаление баз данных перед удалением действующего предохранителя TDE нужно для того, чтобы предотвратить потерю данных из последних транзакций (до 10 минут).

2. Создайте в Key Vault резервную копию материала ключа для предохранителя TDE.
3. Удалите потенциально скомпрометированный ключ из Key Vault.

## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о том, как [заменить предохранитель TDE для сервера в соответствии с требованиями к безопасности с помощью PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- См. дополнительные сведения о [включении прозрачного шифрования данных с помощью собственного ключа из Key Vault с помощью PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
