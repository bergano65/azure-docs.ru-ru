---
title: Список капли с .NET - Лазурное хранилище
description: Узнайте, как перечислить капли в контейнере в учетной записи хранилища Azure с помощью клиентской библиотеки .NET. Примеры кода показывают, как перечислить капли в плоском листинге, или как перечислить капли иерархически, как будто они были организованы в каталоги или папки.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137926"
---
# <a name="list-blobs-with-net"></a>Список капли с .NET

При перечне капли из кода можно указать несколько вариантов управления тем, как результаты возвращаются из хранилища Azure. Можно указать количество результатов для возврата в каждом наборе результатов, а затем получить последующие наборы. Можно указать префикс, чтобы вернуть капли, имена которых начинаются с этого символа или строки. И вы можете перечислить капли в плоской структуре листинга, или иерархически. Иерархический листинг возвращает капли, как будто они были организованы в папки. 

В этой статье показано, как перечислить капли с помощью [клиентской библиотеки Azure Storage для .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-blob-listing-options"></a>Понимание вариантов листинга blob

Чтобы перечислить капли в учетной записи хранилища, позвоните в один из этих методов:

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsСегментированный](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsСегментАсин](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Чтобы перечислить капли в контейнере, позвоните в один из этих методов:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsСегментированный](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsСегментАсин](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Перегрузки для этих методов предоставляют дополнительные возможности для управления тем, как капли возвращаются операцией листинга. Эти варианты описаны в следующих разделах.

### <a name="manage-how-many-results-are-returned"></a>Управление количеством возвращенных результатов

По умолчанию операция листинга возвращает до 5000 результатов одновременно. Чтобы вернуть меньший набор результатов, предоставьте `maxresults` ненулевое значение параметра при вызове одного из методов **ListBlobs.**

Если операция листинга возвращает более 5000 капли, или если `maxresults` вы указали значение для такого, что операция листинга возвращает подмножество контейнеров в учетную запись хранилища, то Azure Storage возвращает *токен продолжения* со списком капли. Токен продолжения — это непрозрачное значение, которое можно использовать для получения следующего набора результатов из хранилища Azure.

В коде проверьте значение токена продолжения, чтобы определить, является ли он недействительным. Когда токен продолжения недействителен, то набор результатов завершен. Если токен продолжения не является нулевым, затем позвоните в операцию листинга снова, передав в токен продолжения, чтобы получить следующий набор результатов, пока токен продолжения не будет нулевым.

### <a name="filter-results-with-a-prefix"></a>Результаты фильтра с префиксом

Чтобы отфильтровать список контейнеров, `prefix` укажите строку для параметра. Строка префикса может включать один или несколько символов. Затем Azure Storage возвращает только капли, имена которых начинаются с этой префикса.

### <a name="return-metadata"></a>Метаданные возврата

Чтобы вернуть метаданные blob с результатами, укажите значение **Метаданных** для перечисления [BlobListingDetails.](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) Azure Storage включает метаданные с возвратом каждой капли, поэтому вам не нужно вызывать один из методов **FetchAttributes** в этом контексте для извлечения метаданных blob.

### <a name="flat-listing-versus-hierarchical-listing"></a>Плоский листинг против иерархического листинга

Blobs в Azure Storage организованы в плоской парадигме, а не в иерархической парадигме (например, в классической файловой системе). Тем не менее, вы можете организовать капли в *виртуальные каталоги* для того, чтобы имитировать структуру папок. Виртуальный каталог является частью названия капли и указывается символом делимитедора.

Чтобы организовать капли в виртуальные каталоги, используйте символ делимитедора в названии blob. Символ делимитера по умолчанию — это передняя слэш (/), но можно указать любой символ в качестве делимитера.

Если вы называете ваши капли с помощью делимитера, то вы можете выбрать список капли иерархически. Для операции иерархического листинга Azure Storage возвращает любые виртуальные каталоги и капли под родительским объектом. Вы можете повторно вызвать операцию листинга, чтобы пройти иерархию, подобно тому, как вы бы программно пересекали классическую файловую систему.

## <a name="use-a-flat-listing"></a>Использование плоского листинга

По умолчанию операция листинга возвращает капли в плоском листинге. В плоском листинге капли не организованы виртуальным каталогом.

В следующем примере перечислены капли в указанном контейнере с помощью плоского списка с уданным дополнительным размером сегмента и записывается имя капли в окне консоли.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

Выход выборки аналогичен:

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Использование иерархического листинга

Когда вы называете операцию листинга иерархически, Azure Storage возвращает виртуальные каталоги и капли на первом уровне иерархии. Свойство [префикса](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) каждого виртуального каталога настроено так, что вы можете пройти префикс в рекурсивном вызове, чтобы получить следующий каталог.

Чтобы перечислить капли иерархически, установите `useFlatBlobListing` параметр метода листинга на **ложный.**

В следующем примере перечислены капли в указанном контейнере с помощью плоского списка с уданным дополнительным размером сегмента и записывается имя капли в окне консоли.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

Выход выборки аналогичен:

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Снимки Blob не могут быть перечислены в иерархической операции листинга.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [List Blobs (Отображение списка BLOB-объектов)](/rest/api/storageservices/list-blobs)
- [Перечисление ресурсов BLOB-объектов](/rest/api/storageservices/enumerating-blob-resources)
