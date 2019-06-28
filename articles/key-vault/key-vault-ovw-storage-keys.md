---
title: Управление ключами учетной записи хранения с хранилищем ключей Azure и Azure CLI
description: Ключи учетной записи хранения обеспечить прозрачную интеграцию между Azure Key Vault и на основе ключей доступа к учетной записи хранения Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 6ac054bc9750e4297080c4ab64030c9c6a5fb55a
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312849"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Управление ключами учетной записи хранения с хранилищем ключей Azure и Azure CLI 

Azure Key Vault управляет ключами для учетных записей хранения Azure и классические учетные записи хранения. Функция учетной записи управляемого хранилища Key Vault можно использовать для выполнения нескольких функций управления ключами для вас.

[Учетная запись хранения Azure](/azure/storage/storage-create-storage-account) использует учетные данные, которые состоят из имени учетной записи и ключа. Ключ создается автоматически и служит в качестве пароля, а не как криптографический ключ. Key Vault управляет ключами учетной записи хранения, сохраняя их в виде [секретов Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Ключи перечислены (Синхронизация) с учетной записью хранения Azure и периодически создаются повторно, или _повернутый_. 

При использовании функции ключа учетной записи управляемого хранилища, необходимо учитывать следующее:

- Значения ключей никогда не возвращаются в ответ на вызывающий объект.
- Только Key Vault должен управлять ключами учетной записи хранения. Не управлять ключами самостоятельно и избежать конфликта с процессами Key Vault.
- Только один объект Key Vault должен управлять ключами учетной записи хранения. Не разрешать управление ключами от нескольких объектов.
- Вы можете запросить Key Vault для управления своей учетной записи хранения с помощью субъекта-пользователя, но не с субъектом-службой.
- Повторное создание ключей только с помощью хранилища ключей. Не восстановить вручную ключи своей учетной записи хранения. 

> [!NOTE]
> Интеграция хранилища Azure с Azure Active Directory (Azure AD) — облачной идентификацией и доступом служба управления корпорации Майкрософт.
> Интеграция Azure AD доступна для [больших двоичных объектов Azure и очереди](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
> Использование Azure AD для аутентификации и авторизации.
> Azure AD предоставляет доступ на базе токенов OAuth2 в службу хранилища Azure так же, как Azure Key Vault.
>
> Azure AD можно выполнять проверку подлинности клиентского приложения с помощью удостоверения приложения или пользователя, а не учетных данных учетной записи.
> Можно использовать [Azure AD управляемое удостоверение](/azure/active-directory/managed-identities-azure-resources/) при запуске в Azure. Управляемые удостоверения устраняют необходимость для проверки подлинности клиента и хранения учетных данных в или с приложением.
> Azure AD использует управление доступом на основе ролей (RBAC) для управления авторизацией, который также поддерживается службой Key Vault.

### <a name="service-principal-application-id"></a>Идентификатор приложения субъекта-службы

Клиент Azure AD предоставляет каждого зарегистрированного приложения с [субъекта-службы](/azure/active-directory/develop/developer-glossary#service-principal-object). Субъект-служба служит в качестве удостоверения приложения (идентификатор). Идентификатор приложения используется во время установки авторизации для доступа к другим ресурсам Azure через RBAC.

Key Vault — это приложение Майкрософт, который предварительно зарегистрирован в всех клиентов Azure AD. Хранилища ключей зарегистрирован под тем же Идентификатором приложения и в каждом облаке Azure.

| Клиенты | Облако | Идентификатор приложения |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Общедоступный Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Другие  | Любой | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Технические условия

Прежде чем использовать хранилище ключей для управления ключ учетной записи хранения, ознакомьтесь с необходимыми условиями:

- Установка [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Создание [учетной записи хранения Azure](https://azure.microsoft.com/services/storage/). Выполните [эти действия](https://docs.microsoft.com/azure/storage/).
- Имя учетной записи хранения необходимо использовать только строчные буквы и цифры. Длина имени должна составлять от 3 до 24 символов.        
      
## <a name="manage-storage-account-keys"></a>Управление ключами учетной записи хранения

Существует четыре основных шага, чтобы использовать хранилище ключей для управления ключами учетной записи хранения.

1. Получите учетную запись хранения.
1. Получите существующее хранилище ключей.
1. Добавьте учетную запись хранения управляемого хранилища ключей в хранилище. Задайте `key1` как активный ключ с помощью повторного создания период в течение 180 дней.
1. Используйте `key1` задать контекст хранилища для указанной учетной записи хранения.

> [!NOTE]
> Key Vault в качестве службы назначается оператор разрешения для учетной записи хранения.
> 
> После настройки хранилища ключей Azure управляемые ключи учетной записи хранения, единственное изменение ключей с помощью хранилища ключей.
> Для ключей учетной записи управляемого хранилища Key Vault управляет смену ключа учетной записи хранения.

1. После создания учетной записи хранения, выполните следующую команду, чтобы получить идентификатор ресурса учетной записи хранения для управления:
    ```
    az storage account show -n storageaccountname
    ```

    Скопируйте значение идентификатора ресурса из выходных данных команды:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Выходные данные примера:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Назначьте роль RBAC «Хранилища учетной записи роль службы оператора ключей» в хранилище ключей. Эта роль ограничивает область доступа к вашей учетной записи хранения. Для классической учетной записи хранения используйте роль «Классического хранилища учетной записи роль службы оператора ключей».

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` — Идентификатор объекта для хранилища ключей в общедоступном облаке Azure. Чтобы получить идентификатор объекта для хранилища ключей в облаке Azure для государственных организаций, см. в разделе [идентификатор приложения субъекта-службы](#service-principal-application-id).
    
1. Создайте ключ хранилища управляемую учетную запись хранения:

    Задайте период повторного создания 90 дней. Через 90 дней, повторно создает хранилище ключей `key1` и меняет местами активного ключа из `key2` для `key1`. `key1` затем помечен как активный ключ. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Создание и генерация маркеры

Вы также можете попросить Key Vault, чтобы создать маркеры подписи общего доступа. Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения. Можно предоставить клиентам доступ к ресурсам в вашей учетной записи хранения без предоставления общего доступа к ключам учетной записи. Подпись общего доступа предоставляет безопасный способ совместного использования ресурсов хранения без ущерба для ключей учетной записи.

Команды в этом разделе выполните следующие действия.

- Создание определения общую учетную запись доступа к подписи `<YourSASDefinitionName>`. Определение устанавливается для учетной записи хранения Key Vault управлять `<YourStorageAccountName>` в хранилище ключей `<VaultName>`.
- Создайте токен подписи общего доступа учетной записи для служб BLOB-объектов, файлов, таблиц и очередей. Маркер создается для типов ресурсов, службы, контейнер и объект. Маркер создается со всеми разрешениями, по протоколу https и с указанными датами начала и окончания.
- Создание определения управляемого хранилища ключей хранилища общего доступа подписи в хранилище. Определение имеет шаблон URI токена подписи общего доступа, который был создан. Определение имеет тип подписи общего доступа `account` и действителен в течение N дней.
- Получить маркер фактического доступа из секрета Key Vault, который соответствует определению подпись общего доступа.

После завершения действия, описанные в предыдущем разделе, выполните следующие команды, чтобы запросить хранилище ключей, чтобы создать маркеры подписи общего доступа. 

1. Создание определения подписи общего доступа. После создания определения подписи общего доступа, попросите Key Vault для создания более общих маркеров подписи доступа. Эта операция требует `storage` и `setsas` разрешения.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Справочные сведения об операции, см. в разделе [az хранилища sas учетной записи создавать-](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) справочной документации.

    После успешного выполнения операции копирования выходных данных.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Используйте `$sasToken` создан предыдущей командой и создать определение подпись общего доступа. Дополнительные сведения о параметрах команды, см. в разделе [Создание определения az keyvault хранения sas](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) справочной документации.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Если пользователь не имеет разрешений для учетной записи хранения, сначала нужно получите идентификатор объекта пользователя:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Извлечение токенов в коде

Выполнять операции с вашей учетной записи хранения путем выборки [общих маркеров доступа подписи](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) из хранилища ключей.

Существует три способа аутентификации в Key Vault:

- Использование удостоверения управляемой службы. Такой подход настоятельно рекомендуется.
- Используйте субъект-службу и сертификат. 
- Используйте субъект-службу и пароль. Такой подход не рекомендуется.

Дополнительные сведения см. в разделе [Azure Key Vault: Основные понятия](key-vault-whatis.md#basic-concepts).

В следующем примере показано, как получить маркеры подписи общего доступа. После создания определения подписи общего доступа, получить токены. 

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

Если истекает срок действия вашего токена подписи общего доступа, выборки токена подписи общего доступа снова из хранилища ключей и обновить код.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Команды интерфейса командной строки Azure

Сведения о командах Azure CLI, которые относятся к учетным записям управляемого хранилища, см. в разделе [az keyvault хранения](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) справочной документации.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [ключи, секреты и сертификаты](https://docs.microsoft.com/rest/api/keyvault/).
- Просмотрите статьи на [блог группы разработчиков Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
