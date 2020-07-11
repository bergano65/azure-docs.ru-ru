---
title: Получение маркеров подписи общего доступа в коде | Azure Key Vault
description: Функция управляемой учетной записи хранения обеспечивает тесную интеграцию между Azure Key Vault и учетной записью хранения Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: e429115ce2624685c413ae252229964feee70137
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232599"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Получение маркеров подписанного URL-адреса в коде

Вы можете управлять учетной записью хранения с помощью маркеров подписанного URL-адрес (SAS), хранящихся в хранилище ключей. Дополнительные сведения см. [в статье предоставление ограниченного доступа к ресурсам службы хранилища Azure с помощью SAS](../../storage/common/storage-sas-overview.md).

В этой статье приведены примеры кода .NET, который извлекает маркер SAS и выполняет с ним операции. Сведения о создании и хранении маркеров SAS см. в разделах [Управление ключами учетной записи хранения с помощью Key Vault и Azure CLI](overview-storage-keys.md) или [Управление ключами учетной записи хранения с помощью Key Vault и Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Примеры кода

В этом примере код извлекает маркер SAS из хранилища ключей, использует его для создания новой учетной записи хранения и создает новый клиент службы BLOB-объектов.

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

Если срок действия маркера подписанного URL-доступа скоро истечет, вы можете получить маркер подписи общего доступа из хранилища ключей и обновить код.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Дальнейшие действия
- Узнайте, как [предоставить ограниченный доступ к ресурсам службы хранилища Azure с помощью SAS](../../storage/common/storage-sas-overview.md).
- Узнайте, как [управлять ключами учетной записи хранения с помощью Key Vault, а также Azure CLI](overview-storage-keys.md) или [Azure PowerShell](overview-storage-keys-powershell.md).
- См. [Примеры ключей управляемой учетной записи хранения](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
