---
title: Создание SAS для делегирования пользователя для контейнера или большого двоичного объекта с помощью .NET (Предварительная версия) — служба хранилища Azure
description: Узнайте, как создать SAS делегирования пользователя с помощью учетных данных Azure Active Directory в службе хранилища Azure с помощью клиентской библиотеки .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c75a13a20c1dbb222db69145e24838deb111fb66
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595208"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Создание SAS для делегирования пользователя для контейнера или большого двоичного объекта с помощью .NET (Предварительная версия)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

В этой статье показано, как использовать учетные данные Azure Active Directory (Azure AD) для создания SAS делегирования пользователя для контейнера или большого двоичного объекта с клиентской библиотекой хранилища Azure для .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Проверка подлинности с помощью библиотеки удостоверений Azure (Предварительная версия)

Клиентская библиотека удостоверений Azure для .NET (Предварительная версия) проверяет подлинность субъекта безопасности. Когда код выполняется в Azure, участник безопасности является управляемым удостоверением для ресурсов Azure.

Если код выполняется в среде разработки, проверка подлинности может выполняться автоматически, или в зависимости от используемых инструментов может потребоваться вход в браузер. Microsoft Visual Studio поддерживает единый вход (SSO), чтобы активная учетная запись пользователя Azure AD автоматически использовалась для проверки подлинности. Дополнительные сведения об единого входа см. [в статье единый вход в приложения](../../active-directory/manage-apps/what-is-single-sign-on.md).

Другие средства разработки могут предложить вам войти в систему через веб-браузер. Можно также использовать субъект-службу для проверки подлинности из среды разработки. Дополнительные сведения см. [в статье Создание удостоверения для приложения Azure на портале](../../active-directory/develop/howto-create-service-principal-portal.md).

После проверки подлинности клиентская библиотека удостоверений Azure получает учетные данные маркера. Затем эти учетные данные маркера инкапсулируются в объекте клиента службы, который вы создадите для выполнения операций с хранилищем Azure. Библиотека полностью обрабатывает это, получая соответствующие учетные данные маркера.

Дополнительные сведения о клиентской библиотеке удостоверений Azure см. в статье [Клиентская библиотека удостоверений Azure для .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Назначение ролей RBAC для доступа к данным

Когда субъект безопасности Azure AD пытается получить доступ к данным большого двоичного объекта, этот субъект безопасности должен иметь разрешения для ресурса. Независимо от того, является ли участник безопасности управляемым удостоверением в Azure или учетной записью пользователя Azure AD, выполняющего код в среде разработки, участнику безопасности должна быть назначена роль RBAC, которая предоставляет доступ к данным большого двоичного объекта в службе хранилища Azure. Сведения о назначении разрешений через RBAC см. в разделе **назначение РОЛЕЙ RBAC для прав доступа** в статье [авторизация доступа к BLOB-объектам и очередям Azure с помощью Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>Установка предварительных версий пакетов

В примерах, приведенных в этой статье, используется последняя Предварительная версия [клиентской библиотеки службы хранилища Azure для хранилища BLOB-объектов](https://www.nuget.org/packages/Azure.Storage.Blobs). Чтобы установить предварительную версию пакета, выполните следующую команду в консоли диспетчера пакетов NuGet:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

В примерах в этой статье также используется последняя Предварительная версия [клиентской библиотеки удостоверений Azure для .NET](https://www.nuget.org/packages/Azure.Identity/) для аутентификации с помощью учетных данных Azure AD. Чтобы установить предварительную версию пакета, выполните следующую команду в консоли диспетчера пакетов NuGet:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="add-using-directives"></a>Добавление директив using

Добавьте в код следующие директивы `using`, чтобы использовать предварительные версии клиентских библиотек Azure Identity и Azure Storage.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Получение учетных данных маркера, прошедшего проверку подлинности

Чтобы получить учетные данные маркера, которые ваш код может использовать для авторизации запросов в службе хранилища Azure, создайте экземпляр класса [дефаултазурекредентиал](/dotnet/api/azure.identity.defaultazurecredential) .

В следующем фрагменте кода показано, как получить учетные данные маркера, прошедшего проверку подлинности, и использовать его для создания клиента службы для хранилища BLOB-объектов:

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Получение ключа делегирования пользователя

Каждый SAS подписывается ключом. Чтобы создать SAS для делегирования пользователей, необходимо сначала запросить ключ делегирования пользователя, который затем используется для подписания SAS. Ключ делегирования пользователя аналогичен ключу учетной записи, используемому для подписания SAS службы или SAS учетной записи, за исключением того, что он использует учетные данные Azure AD. Когда клиент запрашивает ключ делегирования пользователя с помощью маркера OAuth 2,0, служба хранилища Azure Возвращает ключ делегирования пользователя от имени пользователя.

После получения ключа делегирования пользователя можно использовать этот ключ, чтобы создать любое количество подписанных URL для общего доступа пользователя в течение времени существования ключа. Ключ делегирования пользователя не зависит от токена OAuth 2,0, используемого для его получения, поэтому токен не нуждается в продлении, пока ключ остается действительным. Можно указать, что ключ действителен в течение периода до 7 дней.

Используйте один из следующих методов, чтобы запросить ключ делегирования пользователя:

- [жетусерделегатионкэй](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [жетусерделегатионкэйасинк](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Следующий фрагмент кода получает ключ делегирования пользователя и записывает его свойства:

```csharp
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Создание маркера SAS

В следующем фрагменте кода показано создание нового [блобсасбуилдер](/dotnet/api/azure.storage.sas.blobsasbuilder) и указание параметров для SAS делегирования пользователя. Затем фрагмент вызывает [тосаскуерипараметерс](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) для получения строки токена SAS. Наконец, код создает полный универсальный код ресурса (URI), включая адрес ресурса и маркер SAS.

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Пример. получение SAS для делегирования пользователя

В следующем примере метода показан полный код для проверки подлинности субъекта безопасности и создания SAS для делегирования пользователя.

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

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
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Пример. чтение большого двоичного объекта с помощью SAS делегирования пользователя

В следующем примере выполняется проверка SAS делегирования пользователя, созданного в предыдущем примере, из имитации клиентского приложения. Если SAS является допустимым, клиентское приложение может прочитать содержимое большого двоичного объекта. Если SAS является недопустимым, например, если срок его действия истек, служба хранилища Azure возвращает код ошибки 403 (запрещено).

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
    catch (StorageRequestFailedException e)
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

## <a name="see-also"></a>Дополнительные материалы

- [Операция получения ключа делегирования пользователя](/rest/api/storageservices/get-user-delegation-key)
- [Создание SAS для делегирования пользователей (REST API)](/rest/api/storageservices/create-user-delegation-sas)
