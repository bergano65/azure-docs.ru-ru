---
title: Указание предоставленного клиентом ключа для запроса к хранилищу BLOB-объектов с помощью .NET в службе хранилища Azure
description: Узнайте, как указать предоставленный клиентом ключ для запроса к хранилищу BLOB-объектов с помощью .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1c282b1b9a4693da2aa71831a503d443660b65c1
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666641"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Указание предоставленного клиентом ключа для запроса к хранилищу BLOB-объектов с помощью .NET

Клиенты, выполняющие запросы к хранилищу BLOB-объектов Azure, имеют возможность предоставить ключ шифрования для отдельного запроса. Включение ключа шифрования в запрос обеспечивает детальный контроль над параметрами шифрования для операций с хранилищем BLOB-объектов. Предоставленные клиентом ключи (Предварительная версия) можно хранить в Azure Key Vault или в другом хранилище ключей.

В этой статье показано, как указать предоставленный клиентом ключ для запроса с помощью .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Пример. использование предоставленного клиентом ключа для отправки большого двоичного объекта

В следующем примере создается ключ, предоставленный клиентом, и этот ключ используется для отправки большого двоичного объекта. Код отправляет блок, а затем фиксирует список блокировок для записи большого двоичного объекта в службу хранилища Azure.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

- [Шифрование неактивных данных в службе хранилища Azure](../common/storage-service-encryption.md)
- [Авторизация доступа к BLOB-объектам и очередям с Azure Active Directory и управляемыми удостоверениями для ресурсов Azure](../common/storage-auth-aad-msi.md)
