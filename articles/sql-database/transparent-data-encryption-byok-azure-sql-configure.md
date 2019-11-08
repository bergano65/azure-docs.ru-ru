---
title: Включение SQL TDE с Azure Key Vault
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
ms.openlocfilehash: 8668718f33cff1ebc0c8e159c0edcae41de3ad75
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821684"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell и CLI: включение прозрачное шифрование данных с помощью ключа, управляемого клиентом, из Azure Key Vault

В этой статье рассматриваются способы применения ключа из Azure Key Vault для прозрачного шифрования данных (TDE) в Базе данных SQL или хранилище данных. Чтобы получить дополнительные сведения об интеграции Azure Key Vault и поддержке при создании собственных ключей для TDE, см. статью [Прозрачное шифрование данных в Azure SQL. Поддержка создания собственных ключей](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Предварительные требования для PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны.

- Подписка Azure и права администратора для нее.
- [Рекомендуется, но необязательно.] Аппаратный модуль безопасности (HSM) или локальное хранилище ключей для создания локальной копии материала ключа для предохранителя TDE.
- Необходимо установить и запустить Azure PowerShell. 
- Создайте Azure Key Vault и ключи для TDE.
  - [Инструкции для PowerShell и ключа из Key Vault](../key-vault/quick-create-powershell.md).
  - [Инструкции по использованию аппаратного модуля безопасности (HSM) и Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Чтобы использовать хранилище ключей для TDE, обязательно настройте для него следующее свойство:
  - [обратимое удаление](../key-vault/key-vault-ovw-soft-delete.md) и очистка защиты
  - [Как использовать обратимое удаление в Key Vault с помощью PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- Чтобы использовать ключ для TDE, обязательно настройте для него следующие атрибуты:
   - без даты окончания срока действия;
   - не отключено;
   - возможность выполнять операции *получения*, *упаковки ключа* и *распаковки ключа*.

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Шаг 1. Назначение серверу удостоверения Azure AD 

Если у вас есть существующий сервер, выполните следующие действия для присвоения этому серверу удостоверения Azure AD:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

При создании сервера используйте командлет [New-азсклсервер](/powershell/module/az.sql/new-azsqlserver) с тегом-Identity, чтобы добавить удостоверение Azure AD во время создания сервера:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Шаг 2. Предоставление серверу разрешений на доступ к Key Vault

Используйте командлет [Set-азкэйваултакцессполици](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) , чтобы предоставить серверу доступ к хранилищу ключей, прежде чем использовать ключ из него для TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Шаг 3. Добавление на сервер ключа из Key Vault и настройка предохранителя TDE


- Используйте командлет [Get-азкэйваулткэй](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) , чтобы получить идентификатор ключа из хранилища ключей.
- Используйте командлет [Add-азсклсерверкэйваулткэй](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) , чтобы добавить ключ из Key Vault на сервер.
- Используйте командлет [Set-азсклсервертранспарентдатаенкриптионпротектор](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) , чтобы задать ключ в качестве предохранителя TDE для всех ресурсов сервера.
- Используйте командлет [Get-азсклсервертранспарентдатаенкриптионпротектор](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) , чтобы убедиться, что средство защиты TDE настроено как предназначенное.

> [!Note]
> Общая длина имени хранилища ключей и имени ключа не может превышать 94 символа.
> 

>[!Tip]
>Пример идентификатора KeyId из Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Шаг 4. Включение прозрачного шифрования данных 

Чтобы включить TDE, используйте командлет [Set-азсклдатабасетранспарентдатаенкриптион](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) .

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Теперь для базы данных или хранилища данных включено прозрачное шифрование данных с ключом шифрования из Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Шаг 5. Проверка состояния и активности шифрования

Используйте [Get-азсклдатабасетранспарентдатаенкриптион](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) , чтобы получить состояние шифрования и [Get-азсклдатабасетранспарентдатаенкриптионактивити](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) , чтобы проверить ход шифрования для базы данных или хранилища данных.

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Другие полезные командлеты PowerShell

- Чтобы отключить TDE, используйте командлет [Set-азсклдатабасетранспарентдатаенкриптион](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) .

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled"
   ```
 
- Используйте командлет [Get-азсклсерверкэйваулткэй](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) , чтобы получить список ключей Key Vault, добавленных на сервер.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Используйте [Remove-азсклсерверкэйваулткэй](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) для удаления ключа Key Vault с сервера.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Устранение неполадок

При возникновении ошибок выполните следующие проверки.
- Если хранилище ключей найти не удается, убедитесь, что вы находитесь в подписке с помощью командлета [Get-азсубскриптион](/powershell/module/az.accounts/get-azsubscription) .

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Если не удается добавить на сервер новый ключ или указать новый в качестве предохранителя TDE, проверьте следующее:
   - ключ не должен иметь дату окончания срока действия;
   - для ключа должны быть включены операции *получения*, *упаковки ключа* и *распаковки ключа*.

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о том, как заменить предохранитель TDE для сервера в соответствии с требованиями безопасности, приводятся в [этой статье](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Сведения о том, как удалить потенциально скомпрометированный предохранитель TDE в случае угрозы безопасности, вы найдете в [этой статье](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Предварительные требования для CLI

- Подписка Azure и права администратора для нее.
- [Рекомендуется, но необязательно.] Аппаратный модуль безопасности (HSM) или локальное хранилище ключей для создания локальной копии материала ключа для предохранителя TDE.
- Интерфейс командной строки версии 2.0 или более поздней версии. Сведения об установке последней версии и связывании ее с подпиской Azure можно найти в разделе [Установка Azure CLI 1.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 
- Создайте Azure Key Vault и ключи для TDE.
  - [Управление Key Vault с помощью интерфейса командной строки 2.0](../key-vault/key-vault-manage-with-cli2.md)
  - [Инструкции по использованию аппаратного модуля безопасности (HSM) и Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Чтобы использовать хранилище ключей для TDE, обязательно настройте для него следующее свойство:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md) (обратимое удаление).
  - [Как использовать обратимое удаление в Key Vault с помощью CLI](../key-vault/key-vault-soft-delete-cli.md) 
- Чтобы использовать ключ для TDE, обязательно настройте для него следующие атрибуты:
   - без даты окончания срока действия;
   - не отключено;
   - возможность выполнять операции *получения*, *упаковки ключа* и *распаковки ключа*.
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>Шаг 1. Создание сервера с помощью удостоверения Azure AD
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>Нужно, чтобы "principalID" не создавал сервер. На следующем шаге для назначения разрешений хранилища ключей используется идентификатор объекта.
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>Шаг 2. Предоставление логическому серверу SQL Server разрешений Key Vault
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Для следующего шага сохраните URI или идентификатор нового ключа, например https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h.
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Шаг 3. Добавление на сервер ключа из Key Vault и настройка предохранителя TDE
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> Общая длина имени хранилища ключей и имени ключа не может превышать 94 символа.
> 

  
## <a name="step-4-turn-on-tde"></a>Шаг 4. Включение прозрачного шифрования данных 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

Теперь в базе данных или хранилище данных существует включенный TDE с ключом шифрования, управляемым клиентом, в Azure Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Шаг 5. Проверка состояния и активности шифрования

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>Справочник по CLI для SQL

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

