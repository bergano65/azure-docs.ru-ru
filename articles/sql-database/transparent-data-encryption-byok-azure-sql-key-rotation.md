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
ms.date: 03/12/2019
ms.openlocfilehash: 464ea73d9b3d7116205377600ffccee13a9e2dcb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566045"
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

## <a name="prerequisites"></a>предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны.

- В этом практическом руководстве предполагается, что вы уже используете ключ из Azure Key Vault в качестве предохранителя TDE для базы данных SQL Azure или хранилища данных. Изучите статью о [прозрачном шифровании данных с поддержкой создания собственных ключей](transparent-data-encryption-byok-azure-sql.md).
- Необходимо установить и запустить Azure PowerShell.
- [Рекомендуется, но необязательно.] Предварительно создайте материал ключа для предохранителя TDE в аппаратном модуле безопасности (HSM) или локальном хранилище ключей, а затем импортируйте этот материал в Azure Key Vault. Дополнительные сведения вы найдете в [инструкциях по использованию аппаратного модуля безопасности (HSM) и Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="manual-key-rotation"></a>Смена ключей вручную

При смене ключей вручную используются командлеты [Add-азкэйваулткэй](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-азсклсерверкэйваулткэй](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)и [Set-азсклсервертранспарентдатаенкриптионпротектор](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) для добавления совершенно нового ключа, который может быть под новым именем ключа или даже с другим ключом. хранилища. Использование этого подхода поддерживает добавление аналогичного ключа в разные хранилища ключей для поддержки сценариев высокой доступности и географического аварийного восстановления.

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


## <a name="other-useful-powershell-cmdlets"></a>Другие полезные командлеты PowerShell

- Чтобы переключить средство защиты TDE с управляемого Майкрософт на режим BYOK, используйте командлет [Set-азсклсервертранспарентдатаенкриптионпротектор](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Чтобы переключить средство защиты TDE из режима BYOK на управляемые корпорацией Майкрософт, используйте командлет [Set-азсклсервертранспарентдатаенкриптионпротектор](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Следующие шаги

- Сведения о том, как удалить потенциально скомпрометированный предохранитель TDE в случае угрозы безопасности, вы найдете в [этой статье](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

- Начало работы с интеграцией Azure Key Vault и поддержка при создании собственных ключей для TDE. [включении прозрачного шифрования данных с использованием собственного ключа из Key Vault с помощью PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
