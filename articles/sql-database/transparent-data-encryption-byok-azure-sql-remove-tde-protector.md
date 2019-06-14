---
title: PowerShell — удаление предохранителя TDE — База данных SQL Azure | Документация Майкрософт
description: Практическое руководство по реагированию на потенциальную компрометацию предохранителя TDE для базы данных SQL Azure или хранилища данных, для которых настроено прозрачное шифрование данных (TDE) с поддержкой создания собственных ключей (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: ad7e760bf84ee08e3928164432564fb23c10d211
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60330658"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Удаление предохранителя TDE с помощью PowerShell

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но все будущие разработки — для модуля Az.Sql. Для этих командлетов см. в разделе [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле Az и в модуле AzureRm практически идентичны.

- Подписка Azure и права администратора для нее.
- Необходимо установить и запустить Azure PowerShell. 
- В этом практическом руководстве предполагается, что вы уже используете ключ из Azure Key Vault в качестве предохранителя TDE для Базы данных SQL Azure или хранилища данных. См. дополнительные сведения о [прозрачном шифровании данных с поддержкой создания собственных ключей](transparent-data-encryption-byok-azure-sql.md).

## <a name="overview"></a>Обзор

Это практическое руководство описывает, как правильно реагировать на потенциальную компрометацию предохранителя TDE для Базы данных SQL Azure или Хранилища данных SQL, для которых настроено TDE с ключами, управляемыми клиентом, в Azure Key Vault с поддержкой BYOK. См. дополнительные сведения о [поддержке BYOK для TDE](transparent-data-encryption-byok-azure-sql.md). 

Следующие процедуры следует выполнять только в крайних случаях или тестовых средах. Внимательно изучите это практическое руководство, так как удаление используемых предохранителей TDE из Azure Key Vault может привести к **потере данных**. 

Если возникнут любые подозрения по поводу компрометации ключа, например путем несанкционированного доступа пользователей и (или) служб к этому ключу, его следует удалить.

Но при этом не забывайте, что сразу после удаления предохранителя TDE в Key Vault **блокируются все подключения к зашифрованным базам данных на сервере, и эти базы данных переходят в автономный режим, а затем удаляются в течение 24 часов**. Старые резервные копии, зашифрованные с помощью скомпрометированного ключа, становятся недоступными.

Ниже показано, как проверяемый отпечатки средства защиты TDE используется с виртуального журнала файлов (VLF) из заданной базы данных. Отпечаток текущего средства защиты TDE базы данных и идентификатор базы данных можно найти, выполнив: ВЫБЕРИТЕ [database_id],       [encryption_state], [encryptor_type], /*асимметричного ключа означает AKV, управляемых службой ключей означает, что сертификат*/ [encryptor_thumbprint,] из [sys]. [ dm_database_encryption_keys] 
 
Следующий запрос возвращает число VLF и шифратор соответствующие отпечатки используется. Каждый другой отпечаток ссылается на другой ключ в Azure Key Vault (AKV): SELECT * FROM sys.dm_db_log_info (database_id) 

Команды PowerShell Get-AzureRmSqlServerKeyVaultKey предоставляет отпечаток из средства защиты TDE используется в запросе, чтобы можно было видеть какие ключей для обеспечения и какие ключи для удаления в AKV. Только ключи, больше не используется в базе данных, можно безопасно удалить из хранилища ключей Azure.

В этом практическом руководстве рассматриваются два подхода в зависимости от результата, которого вы хотите добиться с помощью реагирования:

- вы можете **сохранить доступность** баз данных SQL Azure и (или) хранилищ данных;
- вы можете **заблокировать доступность** баз данных SQL Azure и (или) хранилищ данных.

## <a name="to-keep-the-encrypted-resources-accessible"></a>Сохранение доступности зашифрованных ресурсов

1. Создайте [ключ в Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Убедитесь, что новый ключ успешно создан в другом хранилище. Нельзя использовать то же хранилище, где размещен скомпрометированный предохранитель TDE, так как управление доступом предоставляется на уровне хранилища.
2. Добавьте новый ключ на сервер с помощью [добавить AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) и [AzSqlServerTransparentDataEncryptionProtector набора](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) командлетов и обновить его в качестве нового средства защиты TDE сервера.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Убедитесь, что сервер и все реплики были обновлены до нового средства защиты прозрачного шифрования данных с помощью [Get AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) командлета. 

   >[!NOTE]
   > Распространение нового предохранителя TDE на все базы данных-источники и базы данных-получатели для обновляемого сервера может занять несколько минут.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Создайте [резервную копию нового ключа](/powershell/module/az.keyvault/backup-azkeyvaultkey) в Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Удалите скомпрометированный ключ из Key Vault с помощью [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) командлета. 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Чтобы восстановить ключ Key Vault в будущем с помощью [AzKeyVaultKey восстановления](/powershell/module/az.keyvault/restore-azkeyvaultkey) командлета:
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Блокирование доступности зашифрованных ресурсов

1. Удалите все базы данных, которые зашифрованы с помощью потенциально скомпрометированного ключа.

   Для файлов базы данных и журналов резервные копии создаются автоматически, поэтому вы всегда сможете восстановить базу данных на определенный момент времени, предоставив нужный ключ. Удаление баз данных перед удалением действующего предохранителя TDE нужно для того, чтобы предотвратить потерю данных из последних транзакций (до 10 минут). 
2. Создайте в Key Vault резервную копию материала ключа для предохранителя TDE.
3. Удалите потенциально скомпрометированный ключ из Key Vault.

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о том, как повернуть предохранитель TDE сервера в соответствии с требованиями безопасности, вы найдете в статье [Смена предохранителя TDE с помощью PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- См. дополнительные сведения о [включении прозрачного шифрования данных с использованием собственного ключа из Key Vault с помощью PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
