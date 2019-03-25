---
ms.assetid: ''
title: Учетная запись хранения, управляемая с помощью Azure Key Vault, — CLI
description: Ключи учетной записи хранения обеспечивают простую интеграцию с Azure Key Vault с возможностью доступа на основе ключа к учетной записи службы хранилища Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: prashanthyv
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 5ec5109aa8079b37015f66443b8ebac905ad2fcb
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370573"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Учетная запись хранения, управляемая с помощью Azure Key Vault, — CLI

> [!NOTE]
> [Интеграция хранилища Azure с Azure Active Directory (Azure AD) теперь доступна в предварительной версии](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Рекомендуем использовать Azure AD для аутентификации и авторизации, которые обеспечивают доступ на базе токенов OAuth2 к службе хранилища Azure, как и Azure Key Vault. Это позволяет следующее:
> - Проверять подлинность клиентского приложения, используя удостоверение приложения или пользователя вместо учетных данных учетной записи хранения. 
> - Использовать [управляемое удостоверение Azure AD](/azure/active-directory/managed-identities-azure-resources/) при запуске в Azure. Управляемые удостоверения полностью устраняют необходимость проверки подлинности клиента и хранения учетных данных в приложении или вместе с ним.
> - Для управления авторизацией, которая также поддерживается с помощью Key Vault, используйте управление доступом на основе ролей (RBAC).

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
- Запрос хранилища ключей для управления своей учетной записи хранения можно сделать, субъекта-пользователя сейчас, а не субъект-службу

В следующем примере показано, как разрешить Key Vault управлять ключами учетной записи хранения.

> [!IMPORTANT]
> Арендатор Azure AD предоставляет каждому зарегистрированному приложению **[субъект-службу](/azure/active-directory/develop/developer-glossary#service-principal-object)**, который используется в качестве удостоверения приложения. Идентификатор приложения субъекта-службы используется при авторизации для доступа к другим ресурсам Azure, посредством управления доступом на основе ролей (RBAC). Key Vault — это приложение корпорации Майкрософт, поэтому оно предварительно зарегистрировано во всех арендаторах Azure AD под одним идентификатором приложения в каждом из следующих облаков Azure:
> - Клиенты Azure AD в облаке Azure для государственных организаций используют идентификатор приложения `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Клиенты Azure AD в общедоступном облаке других облаках Azure используют идентификатор приложения `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

<a name="prerequisites"></a>Технические условия
--------------
1. Установите [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).   
2. [Создайте учетную запись хранения](https://azure.microsoft.com/services/storage/).
    - Следуйте [инструкциям](https://docs.microsoft.com/azure/storage/) по созданию учетной записи хранения.  
    - **Рекомендации по именованию**. Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Пошаговые инструкции по использованию Key Vault для управления ключами учетной записи хранения
--------------------------------------------------------------------------------
В целом выполняются такие этапы:
- Получение учетной записи хранения (уже существующей).
- Получение хранилища ключей (уже существующего).
- Добавление в хранилище управляемой Key Vault учетной записи хранения с активным ключом Key1 и периодом повторного создания 180 дней.
- Настройка контекста хранилища для указанной учетной записи хранения с использованием Key1.

В приведенных ниже инструкциях мы назначаем Key Vault в качестве службы, чтобы у учетной записи хранения были разрешения оператора.

> [!NOTE]
> Обратите внимание, что после настройки ключей учетной записи хранения, которыми управляет Azure Key Vault, их можно изменить **только** через Key Vault. Под управляемыми ключами учетной записи подразумевается, что сменой ключей учетной записи хранения будет управлять Key Vault.

> [!IMPORTANT]
> Арендатор Azure AD предоставляет каждому зарегистрированному приложению **[субъект-службу](/azure/active-directory/develop/developer-glossary#service-principal-object)**, который используется в качестве удостоверения приложения. Идентификатор приложения субъекта-службы используется при авторизации для доступа к другим ресурсам Azure, посредством управления доступом на основе ролей (RBAC). Key Vault — это приложение корпорации Майкрософт, поэтому оно предварительно зарегистрировано во всех арендаторах Azure AD под одним идентификатором приложения в каждом из следующих облаков Azure:
> - Клиенты Azure AD в облаке Azure для государственных организаций используют идентификатор приложения `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Клиенты Azure AD в общедоступном облаке других облаках Azure используют идентификатор приложения `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

> - Сейчас вы можете использовать участника-пользователя, чтобы запросить Key Vault, чтобы управление учетной записью хранения, а не субъект-службу


1. После создания учетной записи хранения выполните следующую команду, чтобы получить идентификатор ресурса учетной записи хранения, которой требуется управлять.

    ```
    az storage account show -n storageaccountname 
    ```
    Поле идентификатора копирования в результирующем выше команды, которое приведено ниже.
    ```
    /subscriptions/0xxxxxx-4310-48d9-b5ca-0xxxxxxxxxx/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    
2. Назначение роли RBAC «Хранилища учетной записи роль службы оператора ключей» в хранилище ключей, ограничив область доступа к вашей учетной записи хранения. Для классической учетной записи хранения используйте «Классического хранилища учетной записи роль службы оператора ключей.»
    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    «93c27d83-f79b-4cb2-8dd4-4aa716542e74» — это идентификатор объекта для хранилища ключей в общедоступном облаке. Чтобы получить идентификатор объекта для хранилища ключей в национальных облаках см. в разделе важные предыдущем разделе
    
3. Создайте управляемую учетную запись хранения в Key Vault.     <br /><br />
   Ниже мы указываем для повторного создания период в 90 дней. Через 90 дней хранилище ключей повторно создаст ключ key1 и установит его в качестве активного вместо key2. Теперь оно пометит Key1 как активный ключ. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<a name="step-by-step-instructions-on-how-to-use-key-vault-to-create-and-generate-sas-tokens"></a>Пошаговые инструкции по созданию маркеров SAS с помощью Key Vault
--------------------------------------------------------------------------------
Key Vault также может создавать маркеры SAS. Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения. С помощью SAS можно предоставить клиентам доступ к ресурсам в учетной записи хранения, не предоставляя общий доступ к ключам учетной записи. Это ключевой момент использования подписанного URL-адреса в приложениях — SAS представляет собой безопасный способ предоставления общего доступа к ресурсам хранилища без ущерба для ключей учетной записи.

Выполнив описанные выше этапы, можно запустить следующие команды, чтобы служба Key Vault создала маркеры SAS. 

Служба выполнит такие задачи:
- Задаст определение SAS учетной записи с именем <YourSASDefinitionName> в управляемой Key Vault учетной записи хранения <YourStorageAccountName> в вашем хранилище <VaultName>. 
- Создаст маркер SAS учетной записи для служб "Большой двоичный объект", "Файл", "Таблица" и "Очередь", для типов ресурсов "Служба", "Контейнер" и "Объект" со всеми разрешениями, по протоколу HTTP и с указанными датами начала и окончания.
- Задаст в хранилище определение SAS для управляемого KeyVault хранилища с URI шаблона в виде созданного выше маркера SAS, с типом SAS account и сроком действия N дней.
- Получит фактический маркер доступа от секрета KeyVault, который соответствует определению SAS.

1. На этом этапе мы создадим определение SAS. После создания этого определения SAS Key Vault сможет создать дополнительные маркеры SAS. Для этой операции необходимо разрешение storage/setsas.

```
$sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
```
См. [дополнительные сведения](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas).

После успешного выполнения этой операции отобразится приблизительно такой результат. Скопируйте его.

```console
   "se=2020-01-01&sp=***"
```

1. На этом этапе мы создадим определение SAS с помощью полученных выходных данных ($sasToken). См. [дополнительные сведения](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters).   

```
az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
```
                        

 > [!NOTE] 
 > Если пользователь не имеет разрешений для учетной записи хранения, мы сначала получим идентификатор объекта пользователя.

 ```
 az ad user show --upn-or-object-id "developer@contoso.com"

 az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
 ```
    
## <a name="fetch-sas-tokens-in-code"></a>Получение маркеров SAS в коде

В этом разделе мы обсудим выполнение операций с вашей учетной записью хранения путем получения [маркеров SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) из Key Vault.

В разделе ниже продемонстрировано, как получить маркеры SAS после создания определения SAS, как описано ранее.

> [!NOTE]
>   Существует 3 способа аутентификации в Key Vault (с которыми можно ознакомиться в разделе об [основных понятиях](key-vault-whatis.md#basic-concepts)):
> - использование Управляемого удостоверения службы (настоятельно рекомендуется);
> - использование субъекта-службы и сертификата; 
> - использование субъекта-службы и пароля (не рекомендуется).

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Если срок действия маркера SAS истекает, нужно еще раз получить маркер SAS из Key Vault и обновить код.

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

### <a name="relevant-azure-cli-commands"></a>Соответствующие команды Azure CLI

[Команды хранилища Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

## <a name="see-also"></a>См. также

- [Сведения о ключах, секретах и сертификатах](https://docs.microsoft.com/rest/api/keyvault/)
- [Официальный блог команды разработчиков Azure Key Vault](https://blogs.technet.microsoft.com/kv/)
