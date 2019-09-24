---
title: 'Версия PowerShell: учетная запись хранения, управляемая с помощью Azure Key Vault'
description: Функция управляемой учетной записи хранения обеспечивает тесную интеграцию между Azure Key Vault и учетной записью хранения Azure.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 225d9b715c56e4813a8e26d881c876e7bd498155
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204216"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Управление ключами учетной записи хранения с помощью Key Vault и Azure PowerShell

Учетная запись хранения Azure использует учетные данные, состоящие из имени учетной записи и ключа. Ключ создается автоматически и используется как пароль, а не как криптографический ключ. Key Vault управляет ключами учетной записи хранения, сохраняя их в виде [Key Vault секретов](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

Вы можете использовать функцию ключа управляемой учетной записи хранения Key Vault, чтобы перечислить ключи (синхронизировать) с помощью учетной записи хранения Azure, а также периодически создавать их (циклически переключать). Вы можете управлять ключами как для учетных записей хранения, так и для классических учетных записей хранения.

При использовании функции ключа управляемой учетной записи хранения учитывайте следующие моменты.

- Значения ключа никогда не возвращаются в ответ на вызывающий объект.
- Ключи учетной записи хранения должны управлять только Key Vault. Не управляйте ключами самостоятельно и Избегайте конфликтов с Key Vault процессами.
- Только один объект Key Vault должен управлять ключами учетной записи хранения. Не разрешать управление ключами из нескольких объектов.
- Вы можете запросить Key Vault управления учетной записью хранения с помощью субъекта-пользователя, но не с субъектом-службой.
- Повторное создание ключей с помощью только Key Vault. Не пересоздавайте ключи учетной записи хранения вручную.

Мы рекомендуем использовать интеграцию службы хранилища Azure с Azure Active Directory (Azure AD), облачной службой управления удостоверениями и доступом Майкрософт. Интеграция Azure AD доступна для [больших двоичных объектов и очередей Azure](../storage/common/storage-auth-aad.md), а также предоставляет доступ на основе маркеров OAuth2 к службе хранилища Azure (как Azure Key Vault).

Azure AD позволяет проверять подлинность клиентского приложения с помощью удостоверения приложения или пользователя, а не учетных данных учетной записи хранения. Вы можете использовать [управляемое удостоверение Azure AD](/azure/active-directory/managed-identities-azure-resources/) при запуске в Azure. Управляемые удостоверения изменяют необходимость проверки подлинности клиента и сохраняют учетные данные в приложении или вместе с ним.

Azure AD использует управление доступом на основе ролей (RBAC) для управления авторизацией, которая также поддерживается Key Vault.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Идентификатор приложения субъекта-службы

Клиент Azure AD предоставляет каждому зарегистрированному приложению субъект- [службу](/azure/active-directory/develop/developer-glossary#service-principal-object). Субъект-служба выступает в качестве идентификатора приложения, который используется во время настройки авторизации для доступа к другим ресурсам Azure через RBAC.

Key Vault — это приложение Майкрософт, которое предварительно зарегистрировано во всех клиентах Azure AD. Key Vault регистрируется в одном и том же ИДЕНТИФИКАТОРе приложения в каждом облаке Azure.

| Клиенты | Облако | ИД приложения |
| --- | --- | --- |
| Azure AD | Azure для государственных организаций | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Общедоступный пиринг Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Другие  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимо сначала выполнить следующие действия.

- [Установите модуль Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Создайте хранилище ключей.](quick-create-powershell.md)
- [Создайте учетную запись хранения Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-powershell). Имя учетной записи хранения должно содержать только строчные буквы и цифры. Длина имени должна составлять от 3 до 24 символов.
      

## <a name="manage-storage-account-keys"></a>Управление ключами учетной записи хранения

### <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure

Проверьте подлинность сеанса PowerShell с помощью командлета [Connect-азаккаунт](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) . 

```azurepowershell-interactive
Connect-AzAccount
```
Если у вас несколько подписок Azure, их можно перечислить с помощью командлета [Get-азсубскриптион](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) и указать подписку, которую вы хотите использовать с командлетом [Set-азконтекст](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) . 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Задание переменных

Сначала задайте переменные, которые будут использоваться командлетами PowerShell, как описано ниже. Не забудьте <YourResourceGroupName>обновить заполнители <YourStorageAccountName>, и <YourKeyVaultName> , а также присвоить параметру $keyVaultSpAppId `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` значение (как указано в [идентификаторе приложения субъекта-службы](#service-principal-application-id)выше).

Мы также будем Azure PowerShell использовать командлеты [Get-азконтекст](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) и [Get-АЗСТОРАЖЕАККАУНТ](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) для получения идентификатора пользователя и контекста учетной записи хранения Azure.

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

### <a name="give-key-vault-access-to-your-storage-account"></a>Предоставление Key Vault доступа к учетной записи хранения

Прежде чем приложение Key Vault сможет получить доступ к ключам учетной записи хранения и возможность управления ими, вы должны авторизовать его доступ к вашей учетной записи хранения. Приложению Key Vault нужны разрешения для *перечисления* и *повторного создания* ключей для учетной записи хранения. Эти разрешения включаются с помощью встроенной роли RBAC [Роль службы оператора ключей учетных записей хранения](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Назначьте эту роль субъекту-службе Key Vault, ограничивая область до учетной записи хранения с помощью командлета Azure PowerShell [New-азролеассигнмент](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) .

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

Используйте командлет Azure PowerShell [Set-азкэйваултакцессполици](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) , чтобы обновить политику доступа Key Vault и предоставить учетной записи хранения разрешения на доступ к ней.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Обратите внимание, что разрешения для учетных записей хранения не доступны на странице "Политики доступа" учетной записи хранения на портале Azure.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Добавление управляемой учетной записи хранения к экземпляру Key Vault

Используйте командлет Azure PowerShell [Add-азкэйваултманажедсторажеаккаунт](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) , чтобы создать управляемую учетную запись хранения в экземпляре Key Vault. Переключатель `-DisableAutoRegenerateKey` НЕ указывает на повторное создание ключей учетной записи хранения.

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

Чтобы периодически Key Vault повторно создать ключи учетной записи хранения, можно использовать командлет Azure PowerShell [Add-азкэйваултманажедсторажеаккаунт](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) , чтобы задать период повторного создания. В этом примере мы устанавливаем период повторного создания в три дня. Через три дня Key Vault повторно создаст Key2 и заменит активный ключ с "key2" на "key1".

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

## <a name="shared-access-signature-tokens"></a>Токены подписи общего доступа

Вы также можете попросить Key Vault создать маркеры подписи общего доступа. Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения. Вы можете предоставить клиентам доступ к ресурсам в вашей учетной записи хранения без предоставления общего доступа к ключам учетной записи. Подписанный URL-доступ обеспечивает безопасный способ предоставления общего доступа к ресурсам хранилища без ущерба для ключей учетной записи.

Команды в этом разделе выполняются в следующих действиях.

- Задайте определение подписанного URL для общего доступа. 
- Создание маркера подписанного URL-имени учетной записи для служб BLOB-объектов, файлов, таблиц и очередей. Маркер создается для служб типов ресурсов, контейнеров и объектов. Маркер создается со всеми разрешениями, по протоколу HTTPS и с указанными датами начала и окончания.
- Задайте определение подписанного общего доступа к хранилищу Key Vault в хранилище. Определение содержит универсальный код ресурса (URI) шаблона созданного маркера подписи общего доступа. Определение имеет тип `account` подписанного общего доступа и является допустимым в течение N дней.
- Убедитесь, что подписанный URL-доступ сохранен в хранилище ключей в качестве секрета.
- 
### <a name="set-variables"></a>Задание переменных

Сначала задайте переменные, которые будут использоваться командлетами PowerShell, как описано ниже. Не забудьте обновить <YourStorageAccountName> заполнители <YourKeyVaultName> и.

Мы также будем использовать командлеты [New-азсторажеконтекст](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) Azure PowerShell, чтобы получить контекст учетной записи хранения Azure.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Создание маркера подписи общего доступа

Создайте определение подписи общего доступа с помощью командлетов Azure PowerShell [New-азсторажеаккаунтсастокен](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) .
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Значение $sasToken будет выглядеть примерно так.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Создание определения подписи общего доступа

Для создания определения подписанного URL-доступа используйте командлет Azure PowerShell [Set-азкэйваултманажедсторажесасдефинитион](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) .  Можно указать имя для `-Name` параметра.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Проверка определения подписи общего доступа

Чтобы убедиться, что определение подписанного URL-доступа сохранено в хранилище ключей, воспользуйтесь командлетом Azure PowerShell [Get-азкэйваултсекрет](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) .

Сначала найдите определение подписи общего доступа в хранилище ключей.

```azurepowershell-interactive
Get-AzKeyVaultSecret -vault-name <YourKeyVaultName>
```

Секрет, соответствующий определению SAS, будет иметь следующие свойства:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Теперь можно использовать командлет [Get-азкэйваултсекрет](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) и свойство Secret `Name` для просмотра содержимого этого секрета.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

В выходных данных этой команды будет показана строка определения SAS.


## <a name="next-steps"></a>Следующие шаги

- [Примеры ключей управляемой учетной записи хранения](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Сведения о ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md)
- [Ссылка на PowerShell для Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
