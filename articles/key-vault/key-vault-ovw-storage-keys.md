---
title: Управление ключами учетной записи хранения с помощью Azure Key Vault и Azure CLI
description: Ключи учетной записи хранения обеспечивают тесную интеграцию между Azure Key Vault и доступом на основе ключей к учетной записи хранения Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 251a7c21b671052a23f6ee18cb4278737464b25c
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744877"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Управление ключами учетной записи хранения с помощью Azure Key Vault и Azure CLI 

Azure Key Vault управляет ключами для учетных записей хранения Azure и классических учетных записей хранения. Вы можете использовать функцию управляемой учетной записи хранения Key Vault для выполнения нескольких функций управления ключами.

[Учетная запись хранения Azure](/azure/storage/storage-create-storage-account) использует учетные данные, которые состоят из имени учетной записи и ключа. Ключ создается автоматически и используется как пароль, а не как криптографический ключ. Key Vault управляет ключами учетной записи хранения, сохраняя их в виде [Key Vault секретов](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Ключи перечислены (синхронизированы) с учетной записью хранения Azure и периодически создаются или _поворачиваются_. 

При использовании функции ключа управляемой учетной записи хранения учитывайте следующие моменты.

- Значения ключа никогда не возвращаются в ответ на вызывающий объект.
- Ключи учетной записи хранения должны управлять только Key Vault. Не управляйте ключами самостоятельно и Избегайте конфликтов с Key Vault процессами.
- Только один объект Key Vault должен управлять ключами учетной записи хранения. Не разрешать управление ключами из нескольких объектов.
- Вы можете запросить Key Vault управления учетной записью хранения с помощью субъекта-пользователя, но не с субъектом-службой.
- Повторное создание ключей с помощью только Key Vault. Не пересоздавайте ключи учетной записи хранения вручную. 

> [!NOTE]
> Интеграция службы хранилища Azure с Azure Active Directory (Azure AD) — это облачная служба управления удостоверениями и доступом Майкрософт.
> Интеграция Azure AD доступна для [больших двоичных объектов и очередей Azure](../storage/common/storage-auth-aad.md).
> Используйте Azure AD для проверки подлинности и авторизации.
> Azure AD предоставляет доступ на основе маркеров OAuth2 к службе хранилища Azure так же, как Azure Key Vault.
>
> Azure AD позволяет проверять подлинность клиентского приложения с помощью удостоверения приложения или пользователя, а не учетных данных учетной записи хранения.
> Вы можете использовать [управляемое удостоверение Azure AD](/azure/active-directory/managed-identities-azure-resources/) при запуске в Azure. Управляемые удостоверения изменяют необходимость проверки подлинности клиента и сохраняют учетные данные в приложении или вместе с ним.
> Azure AD использует управление доступом на основе ролей (RBAC) для управления авторизацией, которая также поддерживается Key Vault.

### <a name="service-principal-application-id"></a>Идентификатор приложения субъекта-службы

Клиент Azure AD предоставляет каждому зарегистрированному приложению субъект- [службу](/azure/active-directory/develop/developer-glossary#service-principal-object). Субъект-служба выступает в качестве удостоверения приложения (ID). Идентификатор приложения используется во время настройки авторизации для доступа к другим ресурсам Azure через RBAC.

Key Vault — это приложение Майкрософт, которое предварительно зарегистрировано во всех клиентах Azure AD. Key Vault регистрируется в одном и том же ИДЕНТИФИКАТОРе приложения и в каждом облаке Azure.

| Клиенты | Облако | ИД приложения |
| --- | --- | --- |
| Azure AD | Azure для государственных организаций | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Общедоступный пиринг Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Другой  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Предварительные требования

Прежде чем использовать Key Vault для управления ключом учетной записи хранения, ознакомьтесь с предварительными требованиями.

- Установка [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Создайте [учетную запись хранения Azure](https://azure.microsoft.com/services/storage/). Выполните следующие [действия](../storage/index.yml).
- Имя учетной записи хранения должно содержать только строчные буквы и цифры. Длина имени должна составлять от 3 до 24 символов.        
      
## <a name="manage-storage-account-keys"></a>Управление ключами учетной записи хранения

Для управления ключами учетной записи хранения необходимо выполнить четыре основных шага Key Vault.

1. Получение существующей учетной записи хранения.
1. Получение существующего хранилища ключей.
1. Добавьте Key Vault управляемую учетную запись хранения в хранилище. Установите `key1` в качестве активного ключа с периодом повторного создания в 90 дней.
1. Используется `key1` для задания контекста хранилища для указанной учетной записи хранения.

> [!NOTE]
> Key Vault как услуга назначены разрешения оператора для вашей учетной записи хранения.
> 
> После настройки ключей управляемой учетной записи хранения Azure Key Vault измените их только с помощью Key Vault.
> Для управляемых ключей учетной записи хранения Key Vault управляет вращением ключа учетной записи хранения.

1. После создания учетной записи хранения выполните следующую команду, чтобы получить идентификатор ресурса для управления учетной записью хранения:
    ```
    az storage account show -n storageaccountname
    ```

    Скопируйте значение идентификатора ресурса из выходных данных команды:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Пример выходных данных:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Назначьте Key Vault роль службы оператора ключей учетной записи хранения. Эта роль ограничивает область доступа учетной записью хранения. Для классической учетной записи хранения используйте роль "роль службы оператора ключей классической учетной записи хранения".

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74`Идентификатор объекта для Key Vault в общедоступном облаке Azure. Чтобы получить идентификатор объекта для Key Vault в облаке Azure для государственных организаций, см. раздел [идентификатор приложения субъекта-службы](#service-principal-application-id).
    
1. Создайте управляемую учетную запись хранения Key Vault:

    Задайте период повторного создания 90 дней. Через 90 дней Key Vault повторно создает `key1` и меняет местами активный ключ с `key2` на `key1`. `key1`затем помечается как активный ключ. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Создание и создание токенов

Вы также можете попросить Key Vault создать маркеры подписи общего доступа. Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения. Вы можете предоставить клиентам доступ к ресурсам в вашей учетной записи хранения без предоставления общего доступа к ключам учетной записи. Подписанный URL-доступ обеспечивает безопасный способ предоставления общего доступа к ресурсам хранилища без ущерба для ключей учетной записи.

Команды в этом разделе выполняются в следующих действиях.

- Задайте определение `<YourSASDefinitionName>`подписанного URL для общего доступа. Определение задается в Key Vault управляемой учетной `<YourStorageAccountName>` записью хранения в `<VaultName>`хранилище ключей.
- Создание маркера подписанного URL-имени учетной записи для служб BLOB-объектов, файлов, таблиц и очередей. Маркер создается для служб типов ресурсов, контейнеров и объектов. Маркер создается со всеми разрешениями, по протоколу HTTPS и с указанными датами начала и окончания.
- Задайте определение подписанного общего доступа к хранилищу Key Vault в хранилище. Определение содержит универсальный код ресурса (URI) шаблона созданного маркера подписи общего доступа. Определение имеет тип `account` подписанного общего доступа и является допустимым в течение N дней.
- Получите фактический маркер доступа из Key Vault секрета, соответствующего определению подписи общего доступа.

После выполнения действий, описанных в предыдущем разделе, выполните следующие команды, чтобы запросить Key Vault создать маркеры подписи общего доступа. 

1. Создайте определение подписи общего доступа. После создания определения подписанного URL-доступа попросите Key Vault создать дополнительные маркеры подписи общего доступа. Для `storage` этой операции требуются разрешения `setsas` и.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Дополнительные сведения об этой операции см. в справочной документации по [созданию и формированию SAS для учетной записи хранения](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) .

    После успешного выполнения операции скопируйте выходные данные.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. `$sasToken` Используйте созданную предыдущей командой команду и создайте определение подписанного URL-доступа. Дополнительные сведения о параметрах команды см. в справочной документации по [AZ keyvault Storage SAS-Definition](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) .
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Если у пользователя нет разрешений на учетную запись хранения, сначала получите идентификатор объекта пользователя:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Извлечение токенов в коде

Выполните операции с учетной записью хранения, изменив [токены подписи общего доступа](../storage/common/storage-dotnet-shared-access-signature-part-1.md) из Key Vault.

Существует три способа проверки подлинности в Key Vault.

- Используйте управляемое удостоверение службы. Этот подход настоятельно рекомендуется.
- Использование субъекта-службы и сертификата. 
- Используйте субъект-службу и пароль. Такой подход не рекомендуется.

Дополнительные сведения см. в [разделе Azure Key Vault: Основные понятия](key-vault-whatis.md#basic-concepts).

В следующем примере показано, как получить токены подписи общего доступа. Токены выбирались после создания определения подписанного URL-доступа. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Если срок действия маркера подписанного URL-доступа скоро истечет, снова получите маркер подписанного URL-доступа из Key Vault и обновите код.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Команды интерфейса командной строки Azure

Дополнительные сведения о Azure CLI командах, которые относятся к управляемым учетным записям хранения, см. в справочной документации по [AZ keyvault Storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) .

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [ключах, секретах и сертификатах](https://docs.microsoft.com/rest/api/keyvault/).
- Ознакомьтесь с статьями в [блоге Azure Key Vault Team](https://blogs.technet.microsoft.com/kv/).
