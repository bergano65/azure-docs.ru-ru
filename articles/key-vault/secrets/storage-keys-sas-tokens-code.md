---
title: Получение маркеров подписанного URL-адреса в коде | Azure Key Vault
description: Функция управляемой учетной записи хранения обеспечивает простую интеграцию между Azure Key Vault и учетной записью хранения Azure.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ca89d06ea0d5e2396c820b25490b30e25c99f10
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002935"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Получение маркеров подписанного URL-адреса в коде

Вы можете управлять учетной записью хранения с помощью маркеров подписанного URL-адреса (SAS), хранящихся в хранилище ключей. Дополнительные сведения см. в статье [Предоставление ограниченного доступа к ресурсам службы хранилища Azure с помощью подписанных URL-адресов (SAS)](../../storage/common/storage-sas-overview.md).

В этой статье приведены примеры кода .NET, который извлекает маркер SAS и выполняет с ним операции. Сведения о создании и хранении маркеров SAS см. в статьях об управлении ключами учетной записи хранения с помощью [Key Vault и Azure CLI](overview-storage-keys.md) или с помощью [Key Vault и Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Примеры кода

В этом примере код извлекает маркер SAS из хранилища ключей, с его помощью создает учетную запись хранения, после чего создает клиент службы BLOB-объектов.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

Если срок действия маркера подписанного URL-адреса должен скоро истечь, вы можете извлечь маркер из хранилища ключей и обновить код.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Дальнейшие действия
- [Предоставление ограниченного доступа к ресурсам службы хранилища Azure с помощью подписанных URL-адресов (SAS)](../../storage/common/storage-sas-overview.md)
- Управление ключами учетной записи хранения с помощью [Key Vault и Azure CLI](overview-storage-keys.md) или с помощью [Key Vault и Azure PowerShell](overview-storage-keys-powershell.md)
- [Примеры ключей управляемой учетной записи хранения](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
