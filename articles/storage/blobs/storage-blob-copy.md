---
title: Копирование большого двоичного объекта с помощью .NET в службе хранилища Azure
description: Узнайте, как скопировать большой двоичный объект в учетную запись хранения Azure с помощью клиентской библиотеки .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9b3dba0041b38d9d59a10eaf80592bab91f65b98
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600284"
---
# <a name="copy-a-blob-with-net"></a>Копирование большого двоичного объекта с помощью .NET

В этой статье показано, как скопировать большой двоичный объект с помощью учетной записи хранения Azure. В нем также показано, как прервать асинхронную операцию копирования. В примере кода используется [Клиентская библиотека службы хранилища Azure для .NET](/dotnet/api/overview/azure/storage/client).

## <a name="about-copying-blobs"></a>Сведения о копировании больших двоичных объектов

При копировании большого двоичного объекта в той же учетной записи хранения это синхронная операция. При копировании между учетными записями это асинхронная операция. Методы [старткопи](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) и [старткопясинк](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) возвращают значение идентификатора копии, которое используется для проверки состояния или отмены операции копирования.

Источником для операции копирования может быть блочный BLOB-объект, добавочный большой двоичный объект, страничный BLOB-объект или моментальный снимок. Если целевой BLOB-объект уже существует, он должен иметь тот же тип BLOB-объекта, что и исходный BLOB-объект. Любой существующий целевой BLOB-объект будет перезаписан. 

Целевой BLOB-объект нельзя изменить во время выполнения операции копирования. Целевой BLOB-объект может иметь только одну операцию незавершенных операций копирования больших двоичных объектов. Иными словами, большой двоичный объект не может быть местом назначения для нескольких ожидающих операций копирования.

Всегда копируется весь исходный BLOB-объект или файл. Копирование диапазона байтов или набора блоков не поддерживается.

При копировании большого двоичного объекта его системные свойства копируются в целевой большой двоичный объект с теми же значениями.

Для всех типов больших двоичных объектов можно проверить свойство [копистате. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) в целевом большом двоичном объекте, чтобы получить состояние операции копирования. Окончательный большой двоичный объект будет зафиксирован после завершения копирования.

Операция копирования может принимать любое из следующих форм:

  - Вы можете скопировать исходный BLOB-объект в целевой большой двоичный объект с другим именем. Целевой BLOB-объект может быть существующим большим двоичным объектом того же типа большого двоичного объекта (блок, добавление или страница) или новым большим двоичным объектом, созданным операцией копирования.
  - Вы можете скопировать исходный BLOB-объект в целевой большой двоичный объект с тем же именем, фактически заменив целевой BLOB-объект. Такая операция копирования удаляет все незафиксированные блоки и перезаписывает метаданные целевого BLOB-объекта.
  - Исходный файл в службе файлов Azure можно скопировать в целевой большой двоичный объект. Целевым объектом BLOB может быть существующий блочный BLOB-объект или новый блочный большой двоичный объект, созданный операцией копирования. Копирование из файлов в страничные BLOB-объекты или добавочные большие двоичные объекты не поддерживается.
  - Можно скопировать моментальный снимок и заменить им исходный большой двоичный объект. Повысив уровень моментального снимка до базового большого двоичного объекта, можно восстановить более раннюю версию большого двоичного объекта.
  - Можно скопировать моментальный снимок в целевой большой двоичный объект с другим именем. Результирующий большой двоичный объект назначения является записываемым BLOB-объектом, а не моментальным снимком.

## <a name="copy-a-blob"></a>Копирование большого двоичного объекта

Чтобы скопировать большой двоичный объект, вызовите один из следующих методов:

 - [старткопи](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [старткопясинк](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

Следующий пример кода возвращает ссылку на созданный ранее BLOB-объект и копирует его в новый большой двоичный объект в том же контейнере:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>Прервать операцию копирования BLOB-объектов

Прерывание операции копирования приводит к получению целевого большого двоичного объекта нулевой длины для блочных BLOB-объектов, добавления больших двоичных объектов и страничных больших двоичных объектов. Однако метаданные для целевого BLOB-объекта будут иметь новые значения, скопированные из исходного большого двоичного объекта или явно заданы в вызове [старткопи](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) или [старткопясинк](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) . Чтобы обеспечить сохранение исходных метаданных перед копированием, Создайте моментальный снимок целевого большого двоичного объекта перед вызовом `StartCopy` или `StartCopyAsync`.

При прерывании текущей операции копирования больших двоичных объектов [копистате. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) целевого большого двоичного объекта имеет значение [копистатус. прервано](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

Методы [аборткопи](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) и [аборткопясинк](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) отменяют текущую операцию копирования больших двоичных объектов и оставляют целевой большой двоичный объект с нулевой длиной и полными метаданными.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

В следующих разделах содержатся сведения о копировании больших двоичных объектов и прерывании текущих операций копирования с помощью интерфейсов API службы Azure.

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Прервать копирование BLOB-объекта](/rest/api/storageservices/abort-copy-blob)
