---
title: Безопасный доступ к данным приложения в облаке с помощью службы хранилища Azure | Документы Майкрософт
description: Защита данных приложения в облаке с помощью маркеров SAS, шифрования и протокола HTTPS.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 05/30/2018
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: 2cd7a83b597d656ddbb0210aacfe96f6c056248b
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986699"
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Безопасный доступ к данным приложения в облаке

Этот учебник представляет первую часть цикла. Вы узнаете, как обеспечить безопасность доступа к учетной записи хранения. 

В третьей части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Использование маркеров SAS для доступа к эскизам изображений
> * Включение шифрования на стороне сервера
> * Включение транспорта только по протоколу HTTPS

[Хранилище BLOB-объектов](../common/storage-introduction.md#blob-storage) представляет собой надежную службу для хранения файлов для приложений. Это руководство дополняет сведения из [предыдущего раздела][previous-tutorial] и описывает процедуру защиты доступа к учетной записи хранения из веб-приложения. По окончании работы изображения шифруются, и для доступа к эскизам веб-приложение использует маркеры безопасности SAS.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимо изучить предыдущее руководство по использованию хранилища — [Автоматическое изменение размера переданных изображений с помощью Сетки событий][previous-tutorial]. 

## <a name="set-container-public-access"></a>Установка общего доступа к контейнеру

В этой части серии учебников для доступа к эскизам используются маркеры SAS. На этом шаге вы установите `off` в качестве значения общего доступа к контейнеру _эскизов_.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Настройка маркеров SAS для эскизов

В первой части этой серии учебников в веб-приложении отображались изображения из общедоступного контейнера. В этой части вы будете использовать маркеры SAS для получения изображений эскизов. Маркеры SAS позволяют предоставлять ограниченный доступ к контейнеру или BLOB-объекту на основе IP-адреса, протокола, интервала времени или имеющихся прав. Дополнительные сведения о подписанных URL-адресах см. в статье об [использование подписанных URL-адресов SAS в службе хранилища Azure](../common/storage-sas-overview.md).

В этом примере репозиторий исходного кода использует ветвь `sasTokens` с примером обновленного кода. Удалите существующее развертывание GitHub с помощью команды [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Затем настройте для веб-приложения развертывание GitHub с помощью команды [az webapp deployment source config](/cli/azure/webapp/deployment/source).  

В следующей команде `<web-app>` — это имя веб-приложения.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

Ветвь `sasTokens` репозитория обновляет файл `StorageHelper.cs`. Она заменяет задачу `GetThumbNailUrls` на пример кода ниже. Обновленная задача получает URL-адреса эскиза путем задания класса [SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy), чтобы указать время начала, время окончания и разрешения для маркера SAS. Развернутое веб-приложение получает эскизы с URL-адресом с помощью токена SAS. В следующем примере показана обновленная задача.
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

В предыдущей задаче используются следующие классы, свойства и методы:

|Класс  |properties| Методы  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)        |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     | |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)     | |[SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)       |
|[BlobContinuationToken](/dotnet/api/microsoft.azure.storage.blob.blobcontinuationtoken)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment)    | [Результаты](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment.results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy)     | [SharedAccessStartTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime)<br>[Разрешения](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.permissions) |        |

## <a name="server-side-encryption"></a>Шифрование на стороне сервера

[Шифрование службы хранилища Azure (SSE)](../common/storage-service-encryption.md) помогает защитить данные. SSE шифрует данные при хранении, выполняя обработку шифрования, расшифровки и управление ключами. Используется 256-битное [шифрование AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), самая надежная технология блочного шифрования на сегодняшний день.

SSE автоматически шифрует данные на всех уровнях производительности ("Стандартный" и "Премиум"), во всех моделях развертывания (Azure Resource Manager и классической) и во всех службах хранилища Azure (больших двоичных объектов, очередей, таблиц и файлов). 

## <a name="enable-https-only"></a>Включение только HTTPS

Чтобы обеспечить безопасность запросов данных в учетную запись хранения и из нее, можно разрешить только запросы HTTPS. Обновите необходимый протокол для учетной записи хранения с помощью команды [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Протестируйте подключение, использующее `curl`, с помощью протокола `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Теперь, когда требуется безопасная передача, появляется следующее сообщение:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Дополнительная информация

В третьей части серии вы узнали, как обеспечить безопасность доступа к учетной записи хранения путем выполнения приведенных ниже задач.

> [!div class="checklist"]
> * Использование маркеров SAS для доступа к эскизам изображений
> * Включение шифрования на стороне сервера
> * Включение транспорта только по протоколу HTTPS

Перейдите к четвертой части, чтобы узнать, как отслеживать приложение облачного хранилища и устранять его неполадки.

> [!div class="nextstepaction"]
> [Мониторинг и устранение неполадок приложения облачного хранилища](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
