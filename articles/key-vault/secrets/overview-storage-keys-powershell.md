---
title: 'Версия PowerShell: учетная запись хранения, управляемая с помощью Azure Key Vault'
description: Функция управляемой учетной записи хранилища обеспечивает бесшовную интеграцию между Хранилищем ключей Azure и учетной записью хранения Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: f8c526148e37ba1b716aafd32dcc3f242358f1eb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427787"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Управление ключами учетной записи хранилища с помощью Key Vault и Azure PowerShell

Учетная запись хранения Azure использует учетные данные, включающие имя учетной записи и ключ. Ключ является автоматически генерируемым и служит паролем, а не криптографическим ключом. Key Vault управляет ключами учетной записи хранилища, храним их как секреты Key Vault. 

Вы можете использовать ключевую функцию учетной записи с управлением хранилищем Key Vault для пересчета (синхронизации) ключей с учетной записью хранения Azure и периодического регенерации (поворота) ключей. Вы можете управлять ключами как для учетных записей хранения, так и для классических учетных записей хранения.

При использовании ключевой функции учетной записи управляемого хранилища следует учитывать следующие моменты:

- Ключевые значения никогда не возвращаются в ответ на абонента.
- Управлять ключами учетной записи хранилища должны только Key Vault. Не управляйте ключами самостоятельно и не вмешивайтесь в процессы Key Vault.
- Управлять ключами учетной записи хранилища должен только один объект Key Vault. Не разрешают управление ключами из нескольких объектов.
- Вы можете запросить Key Vault для управления учетной записью хранилища с помощью основного пользователя, но не с помощью принципала службы.
- Регенерировать ключи, используя только Key Vault. Не создавайте повторно ключи своей учетной записи хранения вручную.

Рекомендуется использовать интеграцию системы хранения данных Azure с Azure Active Directory (Azure AD), облачной службой идентификации и управления доступом корпорации Майкрософт. Интеграция Azure AD доступна для [капли и очередей Azure](../../storage/common/storage-auth-aad.md)и обеспечивает доступ к маркеру OAuth2 к хранилищеAzов (так же, как Azure Key Vault).

Azure AD позволяет проверить подлинность клиентского приложения с помощью приложения или иноеудостоверения пользователя, а не учетных данных учетной записи. При запуске в Azure можно использовать [удостоверение, управляемое Azure AD.](/azure/active-directory/managed-identities-azure-resources/) Управляемые идентификаторы устраняет необходимость в проверке подлинности клиента и хранении учетных данных в приложении или в приложении.

Azure AD использует элементы управления доступом на основе ролей (RBAC) для управления авторизацией, который также поддерживается Key Vault.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Идентификатор основного приложения службы

Арендатор Azure AD предоставляет каждому зарегистрированное приложение [с принципом службы.](/azure/active-directory/develop/developer-glossary#service-principal-object) Директор службы служит идентификатором приложения, который используется во время настройки авторизации для доступа к другим ресурсам Azure через RBAC.

Key Vault — это приложение Майкрософт, предварительно зарегистрированное во всех клиентах Azure AD. Key Vault зарегистрирован в том же идентификаторе приложений в каждом облаке Azure.

| Клиенты | Облако | Идентификатор приложения |
| --- | --- | --- |
| Azure AD | Azure для государственных организаций | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | общедоступный пиринг Azure; | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Другие  | Любой | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого руководства необходимо сначала сделать следующее:

- [Установите модуль Azure PowerShell.](/powershell/azure/install-az-ps?view=azps-2.6.0)
- [Создание хранилища ключей](quick-create-powershell.md)
- [Создайте учетную запись хранения Azure.](../../storage/common/storage-account-create.md?tabs=azure-powershell) Имя учетной записи хранилища должно использоваться только буквы и номера в нижнем регистре. Длина имени должна соться от 3 до 24 символов.
      

## <a name="manage-storage-account-keys"></a>Управление ключами учетной записи хранилища

### <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure

Authenticate сеанс PowerShell с помощью cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 

```azurepowershell-interactive
Connect-AzAccount
```
Если у вас есть несколько подписок Azure, вы можете перечислить их с помощью cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) и указать подписку, которая вы хотите использовать с помощью cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Задание переменных

Во-первых, установите переменные, которые будут использоваться смдлетами PowerShell в следующих шагах. Не забудьте <YourResourceGroupName>обновить <YourStorageAccountName>, <YourKeyVaultName> и заполнителей, и установить $keyVaultSpAppId `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (как указано в [идентификаторе основного приложения службы](#service-principal-application-id), выше).

Мы также будем использовать cmdlets Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) и [Get-AzStorageAccountAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) для получения идентификатора пользователя и контекста вашей учетной записи хранения данных Azure.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>Предоставьте Key Vault доступ к учетной записи хранилища

Прежде чем приложение Key Vault сможет получить доступ к ключам учетной записи хранения и возможность управления ими, вы должны авторизовать его доступ к вашей учетной записи хранения. Приложению Key Vault нужны разрешения для *перечисления* и *повторного создания* ключей для учетной записи хранения. Эти разрешения включаются с помощью встроенной роли RBAC [Роль службы оператора ключей учетных записей хранения](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Присвоите эту роль директору службы Key Vault, ограничивобласть областью для учетной записи хранилища с помощью cmdlet Azure PowerShell [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0)

```azurepowershell-interactive
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

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Предоставление разрешения учетной записи пользователя для управляемых учетных записей хранения

Для обновления политики доступа к Ключу Vault и выдачи разрешений учетной записи на хранение пользователей используйте набор [AzkeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0)

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Обратите внимание, что разрешения для учетных записей хранения не доступны на странице "Политики доступа" учетной записи хранения на портале Azure.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Добавление управляемой учетной записи хранения к экземпляру Key Vault

Для создания управляемой учетной записи хранилища в экземпляре Key Vault используйте Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet. Переключатель `-DisableAutoRegenerateKey` НЕ указывает на повторное создание ключей учетной записи хранения.

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

Если вы хотите периодически регенерировать ключи учетной записи хранилища Key Vault, вы можете использовать cmdlet Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) для установки периода регенерации. В этом примере мы устанавливаем период регенерации в три дня. Через три дня Key Vault восстановит 'key2' и поменяет активный ключ с 'key2' на 'key1'.

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

## <a name="shared-access-signature-tokens"></a>Токены общей подписи доступа

Вы также можете попросить Key Vault создать маркеры общей подписи доступа. Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения. Вы можете предоставить клиентам доступ к ресурсам в вашей учетной записи хранения без обмена ключами учетной записи. Общая подпись доступа предоставляет вам безопасный способ обмена ресурсами хранения без ущерба для ключей учетной записи.

Команды в этом разделе выполняют следующие действия:

- Установите определение общей подписи доступа к учетной записи. 
- Создайте маркер подписи доступа с общим доступом для служб Blob, File, Table и Queue. Токен создается для типов ресурсов Service, Container и Object. Токен создается со всеми разрешениями, над https, а также с указанными датами начала и окончания.
- Установите управляемое хранилище Key Vault, общее определение подписи доступа в хранилище. Определение имеет шаблон URI созданного маркера общей подписи доступа. Определение имеет общий тип `account` подписи доступа и действителен для N дней.
- Убедитесь, что общая подпись доступа была сохранена в хранилище ключей в качестве секрета.
- 
### <a name="set-variables"></a>Задание переменных

Во-первых, установите переменные, которые будут использоваться смдлетами PowerShell в следующих шагах. Обязательно <YourStorageAccountName> обновите <YourKeyVaultName> и заполнителей.

Мы также будем использовать смлеты Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) для получения контекста вашей учетной записи хранения Данных Azure.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Создание маркера общей подписи доступа

Создайте общее определение подписи доступа с помощью cmdlets Azure PowerShell [New-AzStorageAccountSASTotoken.](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0)
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Значение $sasToken будет выглядеть так же.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Создание определения общей подписи доступа

Для создания общего определения подписи доступа используйте набор Azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) cmdlet.  Вы можете уговорить имя вашего выбора по параметру. `-Name`

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Проверить определение общей подписи доступа

Вы можете убедиться, что определение общей подписи доступа хранилось в хранилище ключей с помощью cmdlet Azure PowerShell [Get-AzKeyVaultSecret.](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0)

Во-первых, найдите определение общей подписи доступа в хранилище ключей.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Секрет, соответствующий вашему определению SAS, будет иметь следующие свойства:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Теперь вы можете использовать Смдлет [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) и секретное `Name` свойство для просмотра содержимого этого секрета.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

Вывод этой команды покажет строку определения SAS.


## <a name="next-steps"></a>Дальнейшие действия

- [Примеры ключей управляемой учетной записи хранения](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Ссылка на PowerShell для Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
