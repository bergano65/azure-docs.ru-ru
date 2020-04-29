---
title: Смена предохранителя TDE (PowerShell)
description: Сведения о смене предохранителя прозрачного шифрования данных (TDE) для сервера Azure SQL.
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
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067172"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Смена предохранителя TDE с помощью PowerShell

В этой статье описывается смена ключей для сервера SQL Azure, который использует предохранитель TDE из Azure Key Vault. Смена предохранителя TDE в Azure SQL Server означает переключение на новый асимметричный ключ, защищающий базы данных на сервере. Смена ключа является операцией в сети и может занять несколько секунд, так как при этом выполняется только расшифровка и повторное шифрование ключа шифрования данных базы данных, а не всей базы данных.

В этом руководстве описаны два метода смены предохранителя TDE на сервере.

> [!NOTE]
> Перед любой сменой ключа необходимо возобновить работу приостановленного хранилища данных SQL.

> [!IMPORTANT]
> Не удаляйте предыдущие версии ключа после смены. При смене ключа обычно сохраняются данные, зашифрованные прежними версиями ключа, например резервные копии базы данных.

## <a name="prerequisites"></a>Предварительные условия

- В этом практическом руководстве предполагается, что вы уже используете ключ из Azure Key Vault в качестве предохранителя TDE для базы данных SQL Azure или хранилища данных. Изучите статью о [прозрачном шифровании данных с поддержкой создания собственных ключей](transparent-data-encryption-byok-azure-sql.md).
- Необходимо установить и запустить Azure PowerShell.
- [Рекомендуется, но необязательно.] Предварительно создайте материал ключа для предохранителя TDE в аппаратном модуле безопасности (HSM) или локальном хранилище ключей, а затем импортируйте этот материал в Azure Key Vault. Дополнительные сведения вы найдете в [инструкциях по использованию аппаратного модуля безопасности (HSM) и Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Инструкции по установке модуля Az см. в статье об [установке Azure PowerShell](/powershell/azure/install-az-ps). Конкретные командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager (RM) по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Модуль AzureRM продолжит принимать исправления ошибок до 2020 декабря.  Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны. Дополнительные сведения о совместимости см. [в разделе Введение в новый модуль Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Сведения об установке см. в разделе [Install Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Смена ключей вручную

При смене ключей вручную используются следующие команды, чтобы добавить совершенно новый ключ, который может находиться под новым именем ключа или даже в другом хранилище ключей. Использование этого подхода поддерживает добавление аналогичного ключа в разные хранилища ключей для поддержки сценариев высокой доступности и географического аварийного восстановления.

> [!NOTE]
> Общая длина имени хранилища ключей и имени ключа не может превышать 94 символа.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Используйте командлеты [Add-азкэйваулткэй](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-азсклсерверкэйваулткэй](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)и [Set-азсклсервертранспарентдатаенкриптионпротектор](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Используйте команду [AZ keyvault Key Create](/cli/azure/keyvault/key#az-keyvault-key-create), [AZ SQL Server Key Create](/cli/azure/sql/server/key#az-sql-server-key-create)и [AZ SQL Server TDE-Key Set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) .

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Полезные командлеты PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Чтобы переключить средство защиты TDE с управляемого Майкрософт на режим BYOK, используйте командлет [Set-азсклсервертранспарентдатаенкриптионпротектор](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Чтобы переключить средство защиты TDE из режима BYOK на управляемые корпорацией Майкрософт, используйте командлет [Set-азсклсервертранспарентдатаенкриптионпротектор](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

В следующих примерах используется [AZ SQL Server TDE-Key Set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Чтобы переключить средство защиты TDE с управляемого Майкрософт на режим BYOK,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Чтобы переключить средство защиты TDE из режима BYOK на управляемые корпорацией Майкрософт,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Дальнейшие шаги

- В случае угрозы безопасности Узнайте, как удалить потенциально скомпрометированный предохранитель TDE: [Удаление потенциально скомпрометированного ключа](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Приступая к работе с Azure Key Vault интеграция и поддержка создание собственных ключей для TDE: [Включение TDE с помощью собственного ключа из Key Vault с помощью PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
