---
title: PowerShell — смена предохранителя TDE — База данных SQL Azure | Документация Майкрософт
description: Сведения о смене предохранителя прозрачного шифрования данных (TDE) для сервера Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 03/12/2019
ms.openlocfilehash: 760b292e75b4cc64b85eaf51ffad0521b721dabf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895657"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Смена предохранителя TDE с помощью PowerShell

В этой статье описывается смена ключей для сервера SQL Azure, который использует предохранитель TDE из Azure Key Vault. Смена предохранителя TDE для сервера Azure SQL предусматривает переключение защиты баз данных на сервере на новый асимметричный ключ. Смена ключей выполняется через Интернет буквально за несколько секунд, так как для этой операции достаточно расшифровать и повторно шифровать только ключ шифрования данных, а не всю базу данных.

В этом руководстве описаны два метода смены предохранителя TDE на сервере.

> [!NOTE]
> Перед любой сменой ключа необходимо возобновить работу приостановленного хранилища данных SQL.
>

> [!IMPORTANT]
> После смены **НЕ УДАЛЯЙТЕ** старую версию ключа.  При смене ключа обычно сохраняются данные, зашифрованные прежними версиями ключа, например резервные копии базы данных. 
>

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но все будущие разработки — для модуля Az.Sql. Для этих командлетов см. в разделе [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле Az и в модуле AzureRm практически идентичны.

- В этом практическом руководстве предполагается, что вы уже используете ключ из Azure Key Vault в качестве предохранителя TDE для Базы данных SQL Azure или хранилища данных. Изучите статью о [прозрачном шифровании данных с поддержкой создания собственных ключей](transparent-data-encryption-byok-azure-sql.md).
- Необходимо установить и запустить Azure PowerShell.
- [Рекомендуется, но необязательно.] Предварительно создайте материал ключа для предохранителя TDE в аппаратном модуле безопасности (HSM) или локальном хранилище ключей, а затем импортируйте этот материал в Azure Key Vault. Дополнительные сведения вы найдете в [инструкциях по использованию аппаратного модуля безопасности (HSM) и Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="manual-key-rotation"></a>Смена ключей вручную

Вручную смены ключей использует [добавить AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [добавить AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), и [AzSqlServerTransparentDataEncryptionProtector набора](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) командлеты для добавления совершенно нового ключа, который может иметь имя нового ключа или даже в другом хранилище ключей. Использование этого подхода поддерживает добавление аналогичного ключа в разные хранилища ключей для поддержки сценариев высокой доступности и географического аварийного восстановления.

>[!NOTE]
>Общая длина имени хранилища ключей и имени ключа не может превышать 94 символа.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

## <a name="option-2-manual-rotation"></a>Вариант 2. Смена вручную

Использует параметр [добавить AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey), [добавить AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), и [AzSqlServerTransparentDataEncryptionProtector набора](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) командлеты, чтобы добавить полностью новый ключ, который может иметь имя нового ключа или даже в другом хранилище ключей. 

>[!NOTE]
>Общая длина имени хранилища ключей и имени ключа не может превышать 94 символа.
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Другие полезные командлеты PowerShell

- Чтобы переключить средства защиты прозрачного шифрования данных из управляемых корпорацией Майкрософт в режим BYOK, используйте [AzSqlServerTransparentDataEncryptionProtector набора](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) командлета.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Для переключения средства защиты TDE с BYOK режим для управляемых корпорацией Майкрософт, используйте [AzSqlServerTransparentDataEncryptionProtector набора](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) командлета.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о том, как удалить потенциально скомпрометированный предохранитель TDE в случае угрозы безопасности, вы найдете в [этой статье](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

- Начало работы с интеграцией Azure Key Vault и поддержка при создании собственных ключей для TDE. [включении прозрачного шифрования данных с использованием собственного ключа из Key Vault с помощью PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
