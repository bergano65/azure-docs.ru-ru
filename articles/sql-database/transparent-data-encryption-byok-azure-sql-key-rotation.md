---
title: Вращайте протектор TDE - PowerShell
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067172"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Смена предохранителя TDE с помощью PowerShell

В этой статье описывается смена ключей для сервера SQL Azure, который использует предохранитель TDE из Azure Key Vault. Вращение tDE-протектора сервера Azure S'L означает переход на новый асимметричный ключ, который защищает базы данных на сервере. Ключевое вращение является онлайн-операцией и должно занять всего несколько секунд, поскольку это только расшифровывает и перешифровывает ключ шифрования данных, а не всю базу данных.

В этом руководстве описаны два метода смены предохранителя TDE на сервере.

> [!NOTE]
> Перед любой сменой ключа необходимо возобновить работу приостановленного хранилища данных SQL.

> [!IMPORTANT]
> Не удаляйте предыдущие версии ключа после опрокидывания. При смене ключа обычно сохраняются данные, зашифрованные прежними версиями ключа, например резервные копии базы данных.

## <a name="prerequisites"></a>Предварительные требования

- В этом практическом руководстве предполагается, что вы уже используете ключ из Azure Key Vault в качестве предохранителя TDE для базы данных SQL Azure или хранилища данных. Изучите статью о [прозрачном шифровании данных с поддержкой создания собственных ключей](transparent-data-encryption-byok-azure-sql.md).
- Необходимо установить и запустить Azure PowerShell.
- [Рекомендуется, но необязательно.] Предварительно создайте материал ключа для предохранителя TDE в аппаратном модуле безопасности (HSM) или локальном хранилище ключей, а затем импортируйте этот материал в Azure Key Vault. Дополнительные сведения вы найдете в [инструкциях по использованию аппаратного модуля безопасности (HSM) и Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Инструкции по установке модуля Az см. в статье об [установке Azure PowerShell](/powershell/azure/install-az-ps). Для конкретных cmdlets [см.](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager (RM) по-прежнему поддерживается базой данных Azure S'L, но все будущие разработки предназначены для модуля Az.Sql. Модуль AzureRM будет получать исправления ошибок по крайней мере до декабря 2020 года.  Аргументы для команд в модуле Az и в модулях Azrm существенно идентичны. Подробнее об их совместимости читайте [в новом модуле Azure PowerShell Az.](/powershell/azure/new-azureps-module-az)

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Для установки [см.](/cli/azure/install-azure-cli)

* * *

## <a name="manual-key-rotation"></a>Смена ключей вручную

Ручное вращение ключей использует следующие команды, чтобы добавить совершенно новый ключ, который может быть под новым ключевым именем или даже другим хранилищем ключей. Использование этого подхода поддерживает добавление аналогичного ключа в разные хранилища ключей для поддержки сценариев высокой доступности и географического аварийного восстановления.

> [!NOTE]
> Общая длина имени хранилища ключей и имени ключа не может превышать 94 символа.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Используйте [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Добавить-AzSqlServerKey,](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)и [Set-AzSqlServerTransparentEncryptionEncryptionEncryptionEncryptionEncryptionEncryptionEncryptionEncryptionEncryption.sdlets.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Используйте [создание ключа az keyvault,](/cli/azure/keyvault/key#az-keyvault-key-create) [создайте серверный ключ az sql](/cli/azure/sql/server/key#az-sql-server-key-create)и команды [набора tde-ключ az sql.](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set)

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

- Чтобы переключить протектор TDE из управляемого Microsoft режима BYOK, используйте [Set-AzSqlServerTransparentEncryptionEncryptionEncryptionEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Чтобы переключить протектор TDE из режима BYOK в управляемый Корпорацией Майкрософт, используйте [Set-AzSqlServerTransparentEncryptionEncryptionEncryptionEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

В следующих примерах используется [набор az sql сервера tde-key.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

- Чтобы переключить протектор TDE из управляемого Microsoft в режим BYOK,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Переключить протектор TDE из режима BYOK в управляемый корпорацией Майкрософт,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Дальнейшие действия

- В случае риска для безопасности, узнайте, как удалить потенциально скомпрометированный протектор TDE: [Удалите потенциально скомпрометированный ключ](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Начало работы с интеграцией Azure Key Vault и принесите собственную поддержку ключа для TDE: [Включите TDE, используя свой собственный ключ от Key Vault с помощью PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
