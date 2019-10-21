---
title: Перечисление контейнеров больших двоичных объектов с помощью .NET в службе хранилища Azure
description: Узнайте, как перечислить контейнеры больших двоичных объектов в учетной записи хранения Azure с помощью клиентской библиотеки .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b7e5b0725049fa5de95f435c848502c36a3a1726
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598125"
---
# <a name="list-blob-containers-with-net"></a>Вывод списка контейнеров больших двоичных объектов с помощью .NET

При перечислении контейнеров в учетной записи хранения Azure из кода можно указать несколько параметров для управления возвратом результатов из службы хранилища Azure. В этой статье показано, как перечислить контейнеры с помощью [клиентской библиотеки службы хранилища Azure для .NET](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-container-listing-options"></a>Общие сведения о параметрах перечисления контейнеров

Чтобы вывести список контейнеров в учетной записи хранения, вызовите один из следующих методов.

- [листконтаинерссегментед](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [листконтаинерссегментедасинк](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Перегрузки для этих методов предоставляют дополнительные параметры для управления возвратом контейнеров с помощью операции перечисления. Эти параметры описаны в следующих разделах.

### <a name="manage-how-many-results-are-returned"></a>Управление количеством возвращаемых результатов

По умолчанию операция перечисления возвращает до 5000 результатов за раз. Чтобы вернуть меньший набор результатов, укажите ненулевое значение параметра `maxresults` при вызове одного из методов **листконтаинерсегментед** .

Если ваша учетная запись хранения содержит более 5000 контейнеров или указано значение `maxresults` таким, что операция перечисления возвращает подмножество контейнеров в учетной записи хранения, служба хранилища Azure возвращает *токен продолжения* со списком контейнера. Маркер продолжения — это непрозрачное значение, которое можно использовать для получения следующего набора результатов из службы хранилища Azure.

В коде проверьте значение маркера продолжения, чтобы определить, имеет ли он значение null. Если маркер продолжения имеет значение null, набор результатов будет завершен. Если токен продолжения не равен null, вызовите **листконтаинерссегментед** или **листконтаинерссегментедасинк** еще раз, передав токен продолжения для получения следующего набора результатов, пока маркер продолжения не будет равен null.

### <a name="filter-results-with-a-prefix"></a>Фильтрация результатов с помощью префикса

Чтобы отфильтровать список контейнеров, укажите строку для параметра `prefix`. Строка префикса может содержать один или несколько символов. Служба хранилища Azure возвращает только те контейнеры, имена которых начинаются с этого префикса.

### <a name="return-metadata"></a>Возврат метаданных

Чтобы вернуть метаданные контейнера с результатами, укажите значение **метаданных** для перечисления [контаинерлистингдетаилс](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) . Служба хранилища Azure включает метаданные с каждым возвращенным контейнером, поэтому вам не нужно также вызывать один из методов **FetchAttributes** для получения метаданных контейнера.

## <a name="example-list-containers"></a>Пример. список контейнеров

В следующем примере асинхронно перечисляются контейнеры в учетной записи хранения, которые начинаются с указанного префикса. В примере перечисляются контейнеры с шагом в 5 результатов за раз и используется токен продолжения для получения следующего сегмента результатов. В примере также возвращаются метаданные контейнера с результатами.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Дополнительные материалы

[Список контейнеров](/rest/api/storageservices/list-containers2) 
[перечисление ресурсов BLOB-объектов](/rest/api/storageservices/enumerating-blob-resources)
