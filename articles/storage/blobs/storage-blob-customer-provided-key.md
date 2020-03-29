---
title: Укажите ключ, предоставленный клиентом, по запросу на хранение Blob с помощью .NET - Azure Storage
description: Узнайте, как указать предоставленный клиентом ключ в запросе на хранение Blob с помощью .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807006"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Укажите предоставленный клиентом ключ по запросу на хранение Blob с помощью .NET

Клиенты, обращаюющиеся с запросами на хранение Azure Blob, имеют возможность предоставить ключ шифрования по индивидуальному запросу. Включая ключ шифрования в запросе обеспечивает детальный контроль над настройками шифрования для операций хранения Blob. Предоставляемые клиентами ключи (предварительный просмотр) могут храниться в Хранилище ключей Azure или в другом ключевом магазине.

В этой статье показано, как указать ключ, предоставленный клиентом, по запросу с .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Чтобы узнать больше о том, как проверить подлинность с помощью клиентской библиотеки Azure Identity из Azure Storage, смотрите раздел под названием **Authenticate с библиотекой идентификаторов Azure,** чтобы [разрешить доступ к каплям и очередям с помощью Active Directory Azure и управляемых идентификаторов для ресурсов Azure.](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Пример: Используйте предоставленный клиентом ключ для загрузки капли

Следующий пример создает ключ, предоставленный клиентом, и использует этот ключ для загрузки капли. Код загружает блок, а затем фиксирует список блоков, чтобы записать каплю в Хранилище Azure.

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

- [Шифрование хранилища Azure для данных в состоянии покоя](../common/storage-service-encryption.md)
- [Разрешить доступ к каплям и очередям с помощью active-каталога Azure и управляемых идентификаторов для ресурсов Azure](../common/storage-auth-aad-msi.md)
