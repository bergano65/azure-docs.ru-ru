---
title: Список контейнеров blob с .NET - Хранилище azure
description: Узнайте, как перечислить контейнеры с каплями в учетной записи хранилища Azure с помощью клиентской библиотеки .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 155b8f5d50c7b106daff8dab4df17200b844c988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135910"
---
# <a name="list-blob-containers-with-net"></a>Список контейнеров blob с .NET

При учрежке контейнеров в учетной записи хранилища Azure из кода можно указать несколько вариантов управления тем, как результаты возвращаются из Хранилища Azure. В этой статье показано, как перечислить контейнеры с помощью [клиентской библиотеки Azure Storage для .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)  

## <a name="understand-container-listing-options"></a>Понимание вариантов листинга контейнеров

Чтобы перечислить контейнеры в учетной записи хранилища, позвоните в один из следующих методов:

- [ListContainersСегментированный](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersСегментАСинк](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Перегрузки для этих методов предоставляют дополнительные возможности для управления тем, как контейнеры возвращаются в ходе операции листинга. Эти варианты описаны в следующих разделах.

### <a name="manage-how-many-results-are-returned"></a>Управление количеством возвращенных результатов

По умолчанию операция листинга возвращает до 5000 результатов одновременно. Чтобы вернуть меньший набор результатов, предоставьте `maxresults` ненулевое значение параметра при вызове одного из методов **ListContainerSegmented.**

Если учетная запись хранилища содержит более 5000 контейнеров, `maxresults` или если вы указали значение для такого, что операция листинга возвращает подмножество контейнеров в учетную запись хранилища, то Azure Storage возвращает *токен продолжения* со списком контейнеров. Токен продолжения — это непрозрачное значение, которое можно использовать для получения следующего набора результатов из хранилища Azure.

В коде проверьте значение токена продолжения, чтобы определить, является ли он недействительным. Когда токен продолжения недействителен, то набор результатов завершен. Если токен продолжения не является нулевым, затем позвоните **ВListContainersSegmented** или **ListContainersSegmentedAsync** снова, передав в токен продолжения для получения следующего набора результатов, пока токен продолжения не будет нулевым.

### <a name="filter-results-with-a-prefix"></a>Результаты фильтра с префиксом

Чтобы отфильтровать список контейнеров, `prefix` укажите строку для параметра. Строка префикса может включать один или несколько символов. Затем Azure Storage возвращает только контейнеры, имена которых начинаются с этой префикса.

### <a name="return-metadata"></a>Метаданные возврата

Чтобы вернуть метаданные контейнера с результатами, укажите значение **Метаданных** для [перечисления КонтейнерНых Списков.](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) Azure Storage включает метаданные с каждым возвращенным контейнером, поэтому для извлечения метаданных контейнера вам не нужно вызывать один из методов **FetchAttributes.**

## <a name="example-list-containers"></a>Пример: Контейнеры список

В следующем примере асинхронно перечислены контейнеры в учетной записи хранения, которые начинаются с указанной префикса. В примере перечислены контейнеры с шагом в 5 результатов за раз и используется токен продолжения для получения следующего сегмента результатов. Пример также возвращает метаданные контейнера с результатами.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
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

## <a name="see-also"></a>См. также

[Список контейнеров,](/rest/api/storageservices/list-containers2)
[перечисляющих ресурсы Blob](/rest/api/storageservices/enumerating-blob-resources)
