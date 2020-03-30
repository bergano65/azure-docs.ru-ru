---
title: Удалить tDE протектор - PowerShell
description: Практическое руководство по реагированию на потенциальную компрометацию предохранителя TDE для базы данных SQL Azure или хранилища данных, для которых настроено прозрачное шифрование данных (TDE) с поддержкой создания собственных ключей (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 5a89c3f7d52c5717b902a69e9c64b3fcc422c481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067210"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Удаление предохранителя TDE с помощью PowerShell

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure и права администратора для нее.
- Необходимо установить и запустить Azure PowerShell.
- В этом практическом руководстве предполагается, что вы уже используете ключ из Azure Key Vault в качестве предохранителя TDE для базы данных SQL Azure или хранилища данных. См. дополнительные сведения о [прозрачном шифровании данных с поддержкой создания собственных ключей](transparent-data-encryption-byok-azure-sql.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Инструкции по установке модуля Az см. в статье об [установке Azure PowerShell](/powershell/azure/install-az-ps). Для конкретных cmdlets [см.](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager (RM) по-прежнему поддерживается базой данных Azure S'L, но все будущие разработки предназначены для модуля Az.Sql. Модуль AzureRM будет получать исправления ошибок по крайней мере до декабря 2020 года.  Аргументы для команд в модуле Az и в модулях Azrm существенно идентичны. Подробнее об их совместимости читайте [в новом модуле Azure PowerShell Az.](/powershell/azure/new-azureps-module-az)

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Для установки [см.](/cli/azure/install-azure-cli)

* * *

## <a name="overview"></a>Обзор

Это практическое руководство описывает, как правильно реагировать на потенциальную компрометацию предохранителя TDE для базы данных SQL Azure или хранилища данных SQL, для которых настроено TDE с ключами, управляемыми клиентом, в Azure Key Vault с поддержкой BYOK. См. дополнительные сведения о [поддержке BYOK для TDE](transparent-data-encryption-byok-azure-sql.md).

Следующие процедуры следует выполнять только в крайних случаях или тестовых средах. Тщательно просмотрите руководство по использованию, так как удаляйте активно используемые протекторы TDE из Azure Key Vault, что приведет к **недоступности базы данных.**

Если ключ когда-либо подозревался в компрометации, так что служба или пользователь имел несанкционированный доступ к ключу, лучше удалить ключ.

Имейте в виду, что как только протектор TDE будет удален в Key Vault, в течение 10 минут все зашифрованные базы данных начнут отказывать во всех соединениях с соответствующим сообщением об ошибке и изменят его состояние на [Недоступное.](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector)

Следующие шаги наметить, как проверить TDE Протектора отпечатки пальцев все еще используется виртуальные файлы журнала (VLF) данной базы данных.
Отпечаток пальца текущего протектора базы данных TDE и идентификатор аидной базы данных можно найти при запуске:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

Следующий запрос возвращает VLFs и шифрование соответствующих отпечатков пальцев в использовании. Каждый отпечаток пальца относится к различным ключам в Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Команда PowerShell **Get-AzureRmSqlServerKeyVaultKey** предоставляет отпечаток пальца TDE Protector, используемый в запросе, так что вы можете видеть, какие ключи хранить и какие ключи удалить в AKV. Только ключи, больше не используемые базой данных, можно безопасно удалить из Azure Key Vault.

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Показать ключ **сервера** PowerShell az sql обеспечивает отпечаток пальца TDE Protector, используемый в запросе, так что вы можете видеть, какие ключи хранить и какие ключи удалить в AKV. Только ключи, больше не используемые базой данных, можно безопасно удалить из Azure Key Vault.

* * *

В этом практическом руководстве рассматриваются два подхода в зависимости от результата, которого вы хотите добиться с помощью реагирования:

- Для того, чтобы базы данных Azure S'L / Хранилища данных **были доступны**
- Сделать **недоступные** базы данных Azure S'L / Хранилища данных

## <a name="to-keep-the-encrypted-resources-accessible"></a>Сохранение доступности зашифрованных ресурсов

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Создайте [ключ в Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Убедитесь, что новый ключ успешно создан в другом хранилище. Нельзя использовать то же хранилище, где размещен скомпрометированный предохранитель TDE, так как управление доступом предоставляется на уровне хранилища.

2. Добавьте новый ключ к серверу, используя [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) и [Set-AzSqlServerTransparentEncryptionEncryptionEncryptionEncryptionEncryptionEncryptionEncryptionEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) и обновите его в качестве нового защитника TDE сервера.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Убедитесь, что сервер и любые реплики обновлены до нового протектора TDE с помощью [Get-AzSqlServerTransparentEncryptionEncryptionEncryptionEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet.

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

5. Удалите скомпрометированный ключ из Key Vault с помощью cmdlet [Remove-AzKeyVaultKey.](/powershell/module/az.keyvault/remove-azkeyvaultkey)

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Для восстановления ключа к Key Vault в будущем с помощью смдлета [Restore-AzKeyVaultKey:](/powershell/module/az.keyvault/restore-azkeyvaultkey)

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Для [справки](/cli/azure/keyvault/key)о командах см.

1. Создайте [ключ в Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Убедитесь, что новый ключ успешно создан в другом хранилище. Нельзя использовать то же хранилище, где размещен скомпрометированный предохранитель TDE, так как управление доступом предоставляется на уровне хранилища.

2. Добавьте новый ключ к серверу и обновите его в качестве нового протектора TDE сервера.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Убедитесь, что сервер и любые реплики обновлены до нового протектора TDE.

   > [!NOTE]
   > Распространение нового предохранителя TDE на все базы данных-источники и базы данных-получатели для обновляемого сервера может занять несколько минут.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Создайте резервную копию нового ключа в Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Удалите скомпрометированный ключ из Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Восстановить ключ к Key Vault в будущем.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Блокирование доступности зашифрованных ресурсов

1. Удалите все базы данных, которые зашифрованы с помощью потенциально скомпрометированного ключа.

   Для файлов базы данных и журналов резервные копии создаются автоматически, поэтому вы всегда сможете восстановить базу данных на определенный момент времени, предоставив нужный ключ. Удаление баз данных перед удалением действующего предохранителя TDE нужно для того, чтобы предотвратить потерю данных из последних транзакций (до 10 минут).

2. Создайте в Key Vault резервную копию материала ключа для предохранителя TDE.
3. Удалите потенциально скомпрометированный ключ из Key Vault.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Дальнейшие действия

- См. дополнительные сведения о том, как [заменить предохранитель TDE для сервера в соответствии с требованиями к безопасности с помощью PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- См. дополнительные сведения о [включении прозрачного шифрования данных с помощью собственного ключа из Key Vault с помощью PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
