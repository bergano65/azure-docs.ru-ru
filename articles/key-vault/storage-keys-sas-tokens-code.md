---
title: 'Версия PowerShell: учетная запись хранения, управляемая с помощью Azure Key Vault'
description: Функция управляемой учетной записи хранения обеспечивает тесную интеграцию между Azure Key Vault и учетной записью хранения Azure.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 75561c643c2b8881e4b390be885b77f2fff4971d
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673456"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Получение маркеров подписанного URL-адреса в коде

Вы можете управлять учетной записью хранения с помощью [маркеров подписанного](../storage/common/storage-dotnet-shared-access-signature-part-1.md) URL в хранилище ключей. В этой статье приводятся примеры C# кода, который ИЗВЛЕКАЕТ маркер SAS и выполняет с ним операции.  Сведения о создании и хранении маркеров SAS см. в разделах [Управление ключами учетной записи хранения с помощью Key Vault и Azure CLI](key-vault-ovw-storage-keys.md) или [Управление ключами учетной записи хранения с помощью Key Vault и Azure PowerShell](key-vault-overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Примеры кода

В этом примере код извлекает маркер SAS из хранилища ключей, использует его для создания новой учетной записи хранения и создает новый клиент службы BLOB-объектов.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Если срок действия маркера подписанного URL-доступа скоро истечет, вы можете получить маркер подписи общего доступа из хранилища ключей и обновить код.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Следующие шаги
- Узнайте, как [управлять ключами учетной записи хранения с помощью Key Vault, а также Azure CLI](key-vault-ovw-storage-keys.md) или [Azure PowerShell](key-vault-overview-storage-keys-powershell.md).
- См. [Примеры ключей управляемой учетной записи хранения](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Сведения о ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md)
- [Ссылка на PowerShell для Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
