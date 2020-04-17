---
title: Включите TDE с Помощью ключей Azure Vault
description: Сведения о настройке прозрачного шифрования данных в базе данных SQL Azure и хранилище данных с помощью PowerShell или интерфейса командной строки для шифрования данных при хранении.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 5b1c985eeec9af25ec576f4e2375c417dc376f95
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452763"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell и CLI: Включите прозрачное шифрование данных с помощью ключа, управляемого клиентом, от Azure Key Vault

В этой статье рассматриваются способы применения ключа из Azure Key Vault для прозрачного шифрования данных (TDE) в Базе данных SQL или хранилище данных. Чтобы получить дополнительные сведения об интеграции Azure Key Vault и поддержке при создании собственных ключей для TDE, см. статью [Прозрачное шифрование данных в Azure SQL. Поддержка создания собственных ключей](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Предварительные требования для PowerShell

- Подписка Azure и права администратора для нее.
- [Рекомендуется, но необязательно.] Аппаратный модуль безопасности (HSM) или локальное хранилище ключей для создания локальной копии материала ключа для предохранителя TDE.
- Необходимо установить и запустить Azure PowerShell.
- Создайте Azure Key Vault и ключи для TDE.
  - [Инструкции по использованию аппаратного модуля безопасности (HSM) и Key Vault](../key-vault/keys/hsm-protected-keys.md)
    - Чтобы использовать хранилище ключей для TDE, обязательно настройте для него следующее свойство:
  - [мягкое удаление](../key-vault/general/overview-soft-delete.md) и чистка защиты
- Чтобы использовать ключ для TDE, обязательно настройте для него следующие атрибуты:
   - без даты окончания срока действия;
   - не отключено;
   - возможность выполнять операции *получения*, *упаковки ключа* и *распаковки ключа*.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Инструкции по установке модуля Az см. в статье об [установке Azure PowerShell](/powershell/azure/install-az-ps). Для конкретных cmdlets [см.](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)

Для получения подробной информации о Key Vault см. [инструкции PowerShell от Key Vault](../key-vault/secrets/quick-create-powershell.md) и [как использовать мягкое удаление Key Vault с помощью PowerShell.](../key-vault/general/soft-delete-powershell.md)

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager (RM) по-прежнему поддерживается базой данных Azure S'L, но все будущие разработки предназначены для модуля Az.Sql. Модуль AzureRM будет получать исправления ошибок по крайней мере до декабря 2020 года.  Аргументы для команд в модуле Az и в модулях Azrm существенно идентичны. Подробнее об их совместимости читайте [в новом модуле Azure PowerShell Az.](/powershell/azure/new-azureps-module-az)

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Назначение серверу удостоверения Azure AD

Если у вас есть существующий сервер, выполните следующие действия для присвоения этому серверу удостоверения Azure AD:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Если вы создаете сервер, используйте [cmdlet New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) с тегом -Identity для добавления итога Azure AD во время создания сервера:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Предоставление серверу разрешений на доступ к Key Vault

Используйте cmdlet [Set-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) чтобы предоставить серверу доступ к хранилищу ключей, прежде чем использовать ключ от него для TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Добавление на сервер ключа из Key Vault и настройка предохранителя TDE

- Используйте cmdlet [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) для получения идентификатора ключа из хранилища ключей
- Используйте cmdlet [Add-AzSqlServerKeyVaultKey,](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) чтобы добавить ключ из Убежища ключей на сервер.
- Используйте [Set-AzSqlServerTransparentEncryptionEncryptionEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet, чтобы установить ключ в качестве протектора TDE для всех серверных ресурсов.
- Используйте [Get-AzSqlServerTransparentEncryptionEncryptionEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet, чтобы подтвердить, что протектор TDE был настроен по назначению.

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

Используйте [Set-AzSqlDatabaseTransparentDataEncryptionEncryptionEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet, чтобы включить TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Теперь для базы данных или хранилища данных включено прозрачное шифрование данных с ключом шифрования из Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Проверка состояния и активности шифрования

Используйте [Get-AzSqlDatabaseTransparentDataEncryptionEncryption,](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) чтобы получить состояние шифрования и [Get-AzSqlDatabaseTransparentDataEncryption,](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) чтобы проверить ход шифрования для базы данных или хранилища данных.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы установить требуемую версию интерфейса Command-Line Interface 2.0 или позже и подключиться к подписке Azure, [см. Установить и настроить перекрестный командно-линейный интерфейс Azure 2.0.](https://docs.microsoft.com/cli/azure/install-azure-cli)

Для получения подробной информации о Key Vault [см. Управление ключом Vault с помощью CLI 2.0](../key-vault/general/manage-with-cli2.md) и [Как использовать key Vault с помощью мягкого удаления с ПОМОЩЬю CLI.](../key-vault/general/soft-delete-cli.md)

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Назначение серверу удостоверения Azure AD

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Нужно, чтобы "principalID" не создавал сервер. На следующем шаге для назначения разрешений хранилища ключей используется идентификатор объекта.

## <a name="grant-key-vault-permissions-to-your-server"></a>Предоставление серверу разрешений на доступ к Key Vault

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Для следующего шага сохраните URI или идентификатор нового ключа, например https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h.

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Добавление на сервер ключа из Key Vault и настройка предохранителя TDE

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> Общая длина имени хранилища ключей и имени ключа не может превышать 94 символа.

## <a name="turn-on-tde"></a>Включение прозрачного шифрования данных

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Теперь в базе данных или хранилище данных существует включенный TDE с ключом шифрования, управляемым клиентом, в Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Проверка состояния и активности шифрования

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Полезные командлеты PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Для выключения TDE используйте [Set-AzSqlDatabaseTransparentDataEncryptionEncryptionEncryptionencryption.](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption)

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Для возврата добавленного на сервера ключаключена [получите-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Используйте для удаления ключа Убежища с сервера [для](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) удаления ключа Key Vault.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

- Для общих настроек базы данных [см.](/cli/azure/sql)

- Для настроек ключей хранилища [см.](/cli/azure/sql/server/key)

- Для настроек TDE [см.](/cli/azure/sql/server/tde-key) [az sql db tde](/cli/azure/sql/db/tde)

* * *

## <a name="troubleshooting"></a>Устранение неполадок

При возникновении ошибок выполните следующие проверки.

- Если хранилище ключей не найдено, убедитесь, что вы находитесь в правильной подписке.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Если не удается добавить на сервер новый ключ или указать новый в качестве предохранителя TDE, проверьте следующее:
   - ключ не должен иметь дату окончания срока действия;
   - для ключа должны быть включены операции *получения*, *упаковки ключа* и *распаковки ключа*.

## <a name="next-steps"></a>Следующие шаги

- Узнайте, как повернуть TDE Protector сервера в соответствии с требованиями безопасности: [Поверните протектор шифрования прозрачных данных с помощью PowerShell.](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Сведения о том, как удалить потенциально скомпрометированный предохранитель TDE в случае угрозы безопасности, вы найдете в [этой статье](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).
