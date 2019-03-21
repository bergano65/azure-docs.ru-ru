---
title: 'Версия PowerShell: учетная запись хранения, управляемая с помощью Azure Key Vault'
description: Функция управляемой учетной записи хранения обеспечивает простую интеграцию между Azure Key Vault и учетной записью хранения Azure.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 9b6089aa828b5667f100c1a8cbff3e69345e4512
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405103"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>PowerShell: учетная запись хранения, управляемая с помощью Azure Key Vault

> [!NOTE]
> [Интеграция хранилища Azure с Azure Active Directory (Azure AD) теперь доступна в предварительной версии](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Рекомендуем использовать Azure AD для аутентификации и авторизации, которые обеспечивают доступ на базе токенов OAuth2 к службе хранилища Azure, как и Azure Key Vault. Это позволяет следующее:
> - Проверять подлинность клиентского приложения, используя удостоверение приложения или пользователя вместо учетных данных учетной записи хранения. 
> - Использовать [управляемое удостоверение Azure AD](/azure/active-directory/managed-identities-azure-resources/) при запуске в Azure. Управляемые удостоверения полностью устраняют необходимость проверки подлинности клиента и хранения учетных данных в приложении или вместе с ним.
> - Для управления авторизацией, которая также поддерживается с помощью Key Vault, используйте управление доступом на основе ролей (RBAC).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Учетная запись хранения Azure](/azure/storage/storage-create-storage-account) использует учетные данные, которые состоят из имени учетной записи и ключа. Ключ генерируется автоматически и больше служит паролем, в отличие от криптографического ключа. Key Vault может управлять этими ключами учетных записей хранения, сохраняя их как [секреты Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Обзор

Функция управляемой учетной записи хранения Key Vault выполняет несколько функций управления от вашего имени:

- Создает списки (выполняет синхронизацию) ключей для учетной записи хранения Azure.
- Периодически повторно создает (сменяет) ключи.
- Управляет ключами как для учетных записей хранения, так и для классических учетных записей хранения.
- Значения ключей никогда не возвращаются в ответе вызывающему объекту.

При использовании функции ключей для управляемой учетной записи хранения:

- **Разрешайте только Key Vault управлять ключами учетной записи хранения**. Не пытайтесь управлять ими самостоятельно — это повлияет на процессы Key Vault.
- **Не разрешайте управление ключами учетной записи хранения с помощью более чем одного объекта Key Vault**.
- **Не создавайте повторно ключи своей учетной записи хранения вручную**. Рекомендуем повторно создавать их через Key Vault.

В следующем примере показано, как разрешить Key Vault управлять ключами учетной записи хранения.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Предоставление доступа Key Vault к учетной записи хранения

> [!IMPORTANT]
> Арендатор Azure AD предоставляет каждому зарегистрированному приложению **[субъект-службу](/azure/active-directory/develop/developer-glossary#service-principal-object)**, который используется в качестве удостоверения приложения. Идентификатор приложения субъекта-службы используется при авторизации для доступа к другим ресурсам Azure, посредством управления доступом на основе ролей (RBAC). Key Vault — это приложение корпорации Майкрософт, поэтому оно предварительно зарегистрировано во всех арендаторах Azure AD под одним идентификатором приложения в каждом из следующих облаков Azure:
> - Клиенты Azure AD в облаке Azure для государственных организаций используют идентификатор приложения `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Клиенты Azure AD в общедоступном облаке других облаках Azure используют идентификатор приложения `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

Прежде чем приложение Key Vault сможет получить доступ к ключам учетной записи хранения и возможность управления ими, вы должны авторизовать его доступ к вашей учетной записи хранения. Приложению Key Vault нужны разрешения для *перечисления* и *повторного создания* ключей для учетной записи хранения. Эти разрешения включаются с помощью встроенной роли RBAC [Роль службы оператора ключей учетных записей хранения](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Назначьте эту роль субъекту-службе Key Vault, ограничив область учетной записью хранения. Соответствующие инструкции приведены далее. Обязательно обновите переменные `$resourceGroupName`, `$storageAccountName`, `$storageAccountKey` и `$keyVaultName` перед выполнением скрипта:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

После успешного назначения ролей должен отобразиться результат, аналогичный приведенному ниже.

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Если приложение Key Vault уже было добавлено к роли в вашей учетной записи хранения, сообщение об ошибке *Назначение роли уже существует* отобразится. Можно также проверить назначение роли, используя страницу учетной записи хранения "Управление доступом (IAM)" для портала Azure.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Предоставление разрешения учетной записи пользователя для управляемых учетных записей хранения

>[!TIP] 
> Как Azure AD предоставляет **субъект-службу** для удостоверения приложения, так и **субъект-пользователь** предоставляется для удостоверения пользователя. Субъект-пользователь может затем пройти авторизацию для доступа к Key Vault благодаря разрешениям политики доступа Key Vault.

Используя тот же сеанс PowerShell, обновите политику доступа Key Vault для управляемых учетных записей хранения. Этот шаг применяет разрешения учетной записи хранения к учетной записи пользователя, гарантируя, что вы сможете получить доступ к функции управляемой учетной записи хранения: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

Обратите внимание, что разрешения для учетных записей хранения не доступны на странице "Политики доступа" учетной записи хранения на портале Azure.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Добавление управляемой учетной записи хранения к экземпляру Key Vault

Используя тот же сеанс PowerShell, создайте управляемую учетную запись хранения в экземпляре Key Vault. Переключатель `-DisableAutoRegenerateKey` НЕ указывает на повторное создание ключей учетной записи хранения.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

После успешного добавления учетной записи хранения без повторного создания ключей должен отобразиться результат, аналогичный приведенному ниже.

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Включение повторного создания ключа

Если нужно, чтобы программа Key Vault периодически повторно создавала ключи для учетной записи хранения, вы можете настроить этот период. В следующем примере задан трехдневный период повторного создания. Через три дня Key Vault повторно создаст ключ key1 и установит его в качестве активного вместо key2.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

После успешного добавления учетной записи хранения без повторного создания ключа должен отобразиться результат, аналогичный приведенному ниже.

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>Дальнейшие действия

- [Примеры ключей управляемой учетной записи хранения](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Сведения о ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md)
- [Ссылка на PowerShell для Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
