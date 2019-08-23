---
title: Создание SAS для делегирования пользователя для контейнера или большого двоичного объекта с помощью .NET (Предварительная версия) — служба хранилища Azure
description: Узнайте, как создать SAS делегирования пользователя с помощью учетных данных Azure Active Directory в службе хранилища Azure с помощью клиентской библиотеки .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 98ab93bbec8da17dde93c9c343703838b0279994
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900434"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Создание SAS для делегирования пользователя для контейнера или большого двоичного объекта с помощью .NET (Предварительная версия)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

В этой статье показано, как использовать учетные данные Azure Active Directory (Azure AD) для создания SAS делегирования пользователя для контейнера или большого двоичного объекта с [клиентской библиотекой хранилища Azure для .NET](https://www.nuget.org/packages/Azure.Storage.Blobs).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>Установка предварительных версий пакетов

В примерах, приведенных в этой статье, используется последняя Предварительная версия клиентской библиотеки службы хранилища Azure для хранилища BLOB-объектов. Чтобы установить предварительную версию пакета, выполните следующую команду в консоли диспетчера пакетов NuGet:

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

В примерах в этой статье также используется последняя Предварительная версия [клиентской библиотеки удостоверений Azure для .NET](https://www.nuget.org/packages/Azure.Identity/) для аутентификации с помощью учетных данных Azure AD. Клиентская библиотека удостоверений Azure проверяет подлинность субъекта безопасности. После этого участник безопасности, прошедший проверку подлинности, может создать SAS для делегирования пользователей. Дополнительные сведения о клиентской библиотеке удостоверений Azure см. в статье [Клиентская библиотека удостоверений Azure для .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Чтобы пройти проверку подлинности с помощью учетных данных Azure AD через клиентскую библиотеку удостоверений Azure, используйте субъект-службу или управляемое удостоверение в качестве субъекта безопасности в зависимости от того, где выполняется код. Если код выполняется в среде разработки, используйте субъект-службу в целях тестирования. Если код выполняется в Azure, используйте управляемое удостоверение. В этой статье предполагается, что вы используете код из среды разработки и показываете, как использовать субъект-службу для создания SAS для делегирования пользователей.

Чтобы создать субъект-службу с Azure CLI и назначить роль RBAC, вызовите команду [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Укажите роль доступа к данным службы хранилища Azure для назначения новому субъекту-службе. Роль должна включать действие **Microsoft. Storage/storageAccounts/блобсервицес/женератеусерделегатионкэй** . Дополнительные сведения о встроенных ролях, предоставляемых для службы хранилища Azure, см. [в статье встроенные роли для ресурсов Azure](../../role-based-access-control/built-in-roles.md).

Кроме того, укажите область для назначения роли. Субъект-служба создаст ключ делегирования пользователя, который является операцией, выполняемой на уровне учетной записи хранения, поэтому назначение роли должно быть ограничено уровнем учетной записи хранения, группой ресурсов или подпиской. Дополнительные сведения о разрешениях RBAC для создания SAS для делегирования пользователей см. в разделе **Назначение разрешений с помощью RBAC** статьи [Создание SAS делегирования пользователя (REST API)](/rest/api/storageservices/create-user-delegation-sas).

Если у вас нет достаточных разрешений для назначения роли субъекту-службе, может потребоваться попросить владельца или администратора учетной записи выполнить назначение ролей.

В следующем примере используется Azure CLI для создания нового субъекта-службы и назначения ему роли **модуля чтения данных BLOB-объекта хранилища** с использованием области учетной записи.

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

`az ad sp create-for-rbac` Команда возвращает список свойств субъекта-службы в формате JSON. Скопируйте эти значения, чтобы их можно было использовать для создания необходимых переменных среды на следующем шаге.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Назначений ролей RBAC может потребоваться несколько минут для распространения.

## <a name="set-environment-variables"></a>Настройка переменных среды

Клиентская библиотека удостоверений Azure считывает значения из трех переменных среды во время выполнения для проверки подлинности субъекта-службы. В следующей таблице описывается значение, которое задается для каждой переменной среды.

|Переменная среды|Значение
|-|-
|`AZURE_CLIENT_ID`|Идентификатор приложения для субъекта-службы
|`AZURE_TENANT_ID`|Идентификатор клиента Azure AD субъекта-службы
|`AZURE_CLIENT_SECRET`|Пароль, созданный для субъекта-службы

> [!IMPORTANT]
> После задания переменных среды закройте и снова откройте окно консоли. Если вы используете Visual Studio или другую среду разработки, может потребоваться перезапустить среду разработки, чтобы зарегистрировать новые переменные среды.

## <a name="add-using-directives"></a>Добавление директив using

Добавьте следующие `using` директивы в код, чтобы использовать предварительные версии клиентских библиотек Azure Identity и Azure Storage.

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

## <a name="authenticate-the-service-principal"></a>Проверка подлинности субъекта-службы

Чтобы проверить подлинность субъекта-службы, создайте экземпляр класса [дефаултазурекредентиал](/dotnet/api/azure.identity.defaultazurecredential) . `DefaultAzureCredential` Конструктор считывает переменные среды, созданные ранее.

В следующем фрагменте кода показано, как получить проверенные учетные данные и использовать его для создания клиента службы для хранилища BLOB-объектов.

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

## <a name="example-get-a-user-delegation-sas"></a>Пример: Получение SAS для делегирования пользователей

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

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Пример: Чтение большого двоичного объекта с помощью SAS делегирования пользователя

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

## <a name="see-also"></a>См. также

- [Операция получения ключа делегирования пользователя](/rest/api/storageservices/get-user-delegation-key)
- [Создание SAS для делегирования пользователей (REST API)](/rest/api/storageservices/create-user-delegation-sas)
