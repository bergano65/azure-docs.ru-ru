---
title: Используйте .NET для создания sAS-делегации пользователя для контейнера или капли
titleSuffix: Azure Storage
description: Узнайте, как создать SAS с помощью учетных данных Azure Active Directory с помощью клиентской библиотеки .NET для хранения Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371842"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Создание делегации пользователя SAS для контейнера или капли с помощью .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

В этой статье показано, как использовать учетные данные Azure Active Directory (Azure AD) для создания SAS-сообщества пользователей для контейнера или капли с библиотекой клиентов Azure Storage для .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>Назначить роли RBAC для доступа к данным

Когда принцип безопасности Azure AD пытается получить доступ к данным blob, у этого принципала безопасности должны быть разрешения на ресурс. Независимо от того, является ли принцип безопасности управляемым удостоверением личности в Azure или учетной записью пользователя Azure AD, работающим с кодом в среде разработки, директору безопасности необходимо назначить роль RBAC, которая предоставляет доступ к данным blob в Azure Storage. Для получения информации о назначении разрешений через RBAC см. раздел под названием **Присваивать роли RBAC для прав доступа** в [authorize access to Azure blobs и очередей с помощью Azure Active Directory.](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Чтобы узнать больше о том, как проверить подлинность с помощью клиентской библиотеки Azure Identity из Azure Storage, смотрите раздел под названием **Authenticate с библиотекой идентификаторов Azure,** чтобы [разрешить доступ к каплям и очередям с помощью Active Directory Azure и управляемых идентификаторов для ресурсов Azure.](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)

## <a name="add-using-directives"></a>Добавление директив using

Добавьте `using` в код следующие директивы, чтобы использовать клиентские библиотеки Azure Identity и Azure Storage.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Получить удостоверение подлинности маркера

Чтобы получить учетные данные маркеров, которые код может использовать для авторизации запросов в Хранилище Azure, создайте экземпляр класса [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential)

Следующий фрагмент кода показывает, как получить удостоверение подлинности маркера и использовать его для создания клиента службы для хранения Blob:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Получите ключ от делегации пользователя

Каждый SAS подписывается ключом. Для создания sAS делегации пользователя необходимо сначала запросить ключ делегации пользователя, который затем используется для подписания SAS. Ключ делегации пользователя аналогин ключу учетной записи, используемому для подписания службы SAS или учетной записи SAS, за исключением того, что он опирается на учетные данные Azure AD. Когда клиент запрашивает ключ от делегации пользователя с помощью токена OAuth 2.0, Azure Storage возвращает ключ делегации пользователя от имени пользователя.

Если у вас есть ключ для делегирования пользователя, вы можете использовать этот ключ для создания любого количества подписей общего доступа делегации пользователей в течение всего срока службы ключа. Ключ делегации пользователя не зависит от токена OAuth 2.0, используемого для его приобретения, поэтому токен не должен обновляться до тех пор, пока ключ остается в силе. Вы можете указать, что ключ действителен в течение 7 дней.

Используйте один из следующих методов для запроса ключа делегации пользователя:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Следующий фрагмент кода получает ключ от делегации пользователя и выписывает его свойства:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Создание токена SAS

Следующий фрагмент кода показывает создать новый [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) и предоставить параметры для пользователя делегации SAS. Фрагмент затем вызывает [Параметры ТоСасКуири,](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) чтобы получить строку маркера SAS. Наконец, код создает полный URI, включая адрес ресурса и токен SAS.

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

// Use the key to get the SAS token.
string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

// Construct the full URI, including the SAS token.
UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Пример: Получить делегацию пользователя SAS

Следующий пример метода показывает полный код для проверки подлинности принципала безопасности и создания делегации пользователя SAS:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Пример: Прочитайте каплю с делегацией пользователя SAS

Следующий пример тестирует делегацию пользователей SAS, созданную в предыдущем примере из смоделированного клиентского приложения. Если SAS действителен, клиентское приложение может прочитать содержимое капли. Если SAS недействителен, например, если срок ее действия истек, Azure Storage возвращает код ошибки 403 (Запрещено).

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid,
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>См. также

- [Предоставляетограниченный доступ к ресурсам хранения Azure с помощью общих подписей доступа (SAS)](../common/storage-sas-overview.md)
- [Получите операцию ключ к делегированию пользователя](/rest/api/storageservices/get-user-delegation-key)
- [Создание делегации пользователей SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
