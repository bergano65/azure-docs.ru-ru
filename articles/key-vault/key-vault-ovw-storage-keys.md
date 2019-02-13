---
ms.assetid: ''
title: Учетная запись хранения, управляемая с помощью Azure Key Vault, — CLI
description: Ключи учетной записи хранения обеспечивают простую интеграцию между Azure Key Vault и доступом по ключу к учетной записи хранения Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 152e1e5892e3a72286205c2f5bf4e18b2a2bcbf7
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814849"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Учетная запись хранения, управляемая с помощью Azure Key Vault, — CLI

> [!NOTE]
> [Интеграция хранилища Azure с Azure Active Directory (Azure AD) теперь доступна в предварительной версии](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Рекомендуем использовать Azure AD для аутентификации и авторизации, которые обеспечивают доступ на базе токенов OAuth2 к службе хранилища Azure, как и Azure Key Vault. Это позволяет следующее:
> - Проверять подлинность клиентского приложения, используя удостоверение приложения или пользователя вместо учетных данных учетной записи хранения. 
> - Использовать [управляемое удостоверение Azure AD](/azure/active-directory/managed-identities-azure-resources/) при запуске в Azure. Управляемые удостоверения полностью устраняют необходимость проверки подлинности клиента и хранения учетных данных в приложении или вместе с ним.
> - Для управления авторизацией, которая также поддерживается с помощью Key Vault, используйте управление доступом на основе ролей (RBAC).

- Служба хранилища Azure Key Vault управляет ключами учетной записи хранения Azure (ASA).
    - На внутреннем уровне Azure Key Vault может выводить список (синхронизировать) ключи с помощью учетной записи хранения Azure.    
    - Azure Key Vault периодически повторно создает (сменяет) ключи.
    - Значения ключей никогда не возвращаются в ответе вызывающему объекту.
    - Azure Key Vault управляет ключами как учетных записей хранения, так и классических учетных записей хранения.
    
> [!IMPORTANT]
> Арендатор Azure AD предоставляет каждому зарегистрированному приложению **[субъект-службу](/azure/active-directory/develop/developer-glossary#service-principal-object)**, который используется в качестве удостоверения приложения. Идентификатор приложения субъекта-службы используется при авторизации для доступа к другим ресурсам Azure, посредством управления доступом на основе ролей (RBAC). Key Vault — это приложение корпорации Майкрософт, поэтому оно предварительно зарегистрировано во всех арендаторах Azure AD под одним идентификатором приложения в каждом из следующих облаков Azure:
> - Клиенты Azure AD в облаке Azure для государственных организаций используют идентификатор приложения `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Клиенты Azure AD в общедоступном облаке других облаках Azure используют идентификатор приложения `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

<a name="prerequisites"></a>Предварительные требования
--------------
1. Установите [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).   
2. [Создайте учетную запись хранения](https://azure.microsoft.com/services/storage/).
    - Следуйте [инструкциям](https://docs.microsoft.com/azure/storage/) по созданию учетной записи хранения.  
    - **Рекомендации по именованию**. Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Пошаговые инструкции по использованию Key Vault для управления ключами учетной записи хранения
--------------------------------------------------------------------------------
В приведенных ниже инструкциях мы назначаем Key Vault в качестве службы, чтобы у учетной записи хранения были разрешения оператора.

> [!NOTE]
> Обратите внимание, что после настройки ключей учетной записи хранения, которыми управляет Azure Key Vault, их можно изменить **только** через Key Vault. Под управляемыми ключами учетной записи подразумевается, что сменой ключей учетной записи хранения будет управлять Key Vault.

1. После создания учетной записи хранения выполните следующую команду, чтобы получить идентификатор ресурса учетной записи хранения, которой требуется управлять.

    ```
    az storage account show -n storageaccountname 
    ```
    Скопируйте поле идентификатора из результата приведенной выше команды.
    
2. Получите идентификатор объекта субъекта-службы Azure Key Vault, выполнив следующую команду.

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
    После успешного завершения этой команды найдите идентификатор объекта в результате.
    ```console
        {
            ...
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
            ...
        }
    ```
    
3. Присвойте идентификатору Azure Key Vault роль оператора учетной записи хранения.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Создайте управляемую учетную запись хранения в Key Vault.     <br /><br />
   Ниже мы указываем для повторного создания период в 90 дней. Через 90 дней хранилище ключей повторно создаст ключ key1 и установит его в качестве активного вместо key2. Теперь оно пометит Key1 как активный ключ. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```
    Если пользователь не создал учетную запись хранения и не имеет разрешений для доступа к ней, выполнив описанные ниже действия, можно установить разрешения для учетной записи, чтобы обеспечить возможность управления всеми разрешениями хранилища в Key Vault.
    
 > [!NOTE] 
 > Если пользователь не имеет разрешений для учетной записи хранения, мы сначала получим идентификатор объекта пользователя.


    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    
    ```
    
## <a name="how-to-access-your-storage-account-with-sas-tokens"></a>Как получить доступ к учетной записи хранения с помощью маркеров SAS

В этом разделе мы обсудим выполнение операций с вашей учетной записью хранения путем получения [маркеров SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) из Key Vault.

Ниже показано, как получить ключ учетной записи хранения, который находится в Key Vault, и использовать его для создания определения SAS для вашей учетной записи хранения.

> [!NOTE] 
  Существует 3 способа аутентификации в Key Vault (с которыми можно ознакомиться в разделе об [основных понятиях](key-vault-whatis.md#basic-concepts)):
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


### <a name="relavant-azure-cli-cmdlets"></a>Соответствующие командлеты Azure CLI
[Командлеты Azure CLI для службы хранилища](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>Соответствующие командлеты PowerShell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>См. также

- [Сведения о ключах, секретах и сертификатах](https://docs.microsoft.com/rest/api/keyvault/)
- [Официальный блог команды разработчиков Azure Key Vault](https://blogs.technet.microsoft.com/kv/)
