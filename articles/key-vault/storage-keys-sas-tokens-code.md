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
ms.openlocfilehash: cbd7bd034c5cbbdf5308ec660a96dc52a9a2b310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78200708"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Получение маркеров подписанного URL-адреса в коде

Вы можете управлять учетной записью хранилища с [помощью маркеров общей подписи доступа](../storage/common/storage-dotnet-shared-access-signature-part-1.md) в хранилище ключей. В этой статье приводятся примеры кода C,, который получает токен SAS и выполняет с ним операции.  Для получения информации о том, как создавать и хранить токены SAS, можно узнать [ключи учетной записи Управления с помощью Key Vault и ключей учетной](key-vault-ovw-storage-keys.md) записи Azure CLI или [управлять ключами хранения с помощью Key Vault и Azure PowerShell.](key-vault-overview-storage-keys-powershell.md)

## <a name="code-samples"></a>Примеры кода

В этом примере код извлекает токен SAS из хранилища ключей, использует его для создания новой учетной записи хранения и создает новый клиент службы Blob.  

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

Если срок действия маркера общей подписи доступа истекает, можно получить токен подписи общего доступа из хранилища ключей и обновить код.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Дальнейшие действия
- Узнайте, как [управлять ключами учетной записи хранилища с помощью Key Vault и Azure CLI](key-vault-ovw-storage-keys.md) или [Azure PowerShell.](key-vault-overview-storage-keys-powershell.md)
- Посмотреть [примеры ключей учетной записи управляемого хранилища](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [О ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md)
- [Ссылка на PowerShell для Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
