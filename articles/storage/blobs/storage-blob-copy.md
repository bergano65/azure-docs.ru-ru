---
title: Копировать каплю с помощью .NET - Лазурное хранилище
description: Узнайте, как скопировать каплю в учетной записи хранилища Azure с помощью клиентской библиотеки .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135893"
---
# <a name="copy-a-blob-with-net"></a>Копировать каплю с помощью .NET

В этой статье показано, как скопировать каплю с учетной записью хранения Azure. Он также показывает, как прервать асинхронную операцию копирования. В примере код использует [клиентскую библиотеку Azure Storage для .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="about-copying-blobs"></a>О копировании капли

Когда вы копируете каплю в одной учетной записи хранилища, это синхронная операция. При копировании учетных записей это асинхронная операция. Методы [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) и [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) возвращают идентификатор ный экземпляр, используемое для проверки состояния или прерывания операции копии.

Источником капли для операции копирования может быть блок капли, приложение blob, капля страницы, или снимок. Если BLOB-объект назначения уже существует, он должен иметь тот же тип BLOB-объекта, что и BLOB-объект источника. Все существующие BLOB-объекты назначения будут перезаписаны. 

Капля назначения не может быть изменена во время выполнения операции копирования. BLOB-объект назначения может иметь только одну необработанную операцию копирования BLOB-объекта. Другими словами, капля не может быть местом назначения для нескольких ожидающих операций копирования.

Весь исходный каброй или файлвсегда копируется. Копирование ряда байтов или набора блоков не поддерживается.

Когда капля скопирована, ее системные свойства копируются к каплей назначения с теми же значениями.

Для всех типов blob, вы можете проверить [свойство CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) на blob назначения для того чтобы получить состояние деятельности копии. После завершения копирования конечный BLOB-объект будет зафиксирован.

Операция копирования может принимать любую из следующих форм:

  - Можно копировать BLOB-объект источника в BLOB-объект назначения с другим именем. Капля назначения может быть существующей каплей того же типа капли (блок, приложение или страница), или может быть новой каплей, созданной операцией копирования.
  - Вы можете скопировать исходный капля к кабро назначения с тем же именем, эффективно заменяя каброе назначения. Такая операция копирования удаляет любые незафиксированные блоки и перезаписывает метаданные destination blob.
  - Вы можете скопировать исходный файл в службе Azure File в пункт назначения. Цель наяплю может быть существующим блоком blob, или может быть новым блоком blob, созданным в результате операции копирования. Копирование из файлов на страницы капли или приложения капли не поддерживается.
  - Можно скопировать моментальный снимок и заменить им исходный большой двоичный объект. Повысив уровень моментального снимка до базового большого двоичного объекта, можно восстановить более раннюю версию большого двоичного объекта.
  - Можно скопировать моментальный снимок в целевой большой двоичный объект с другим именем. Результирующий целевой большой двоичный объект не станет моментальным снимком, а будет большим двоичным объектом, доступным для записи.

## <a name="copy-a-blob"></a>Копировать каплю

Чтобы скопировать каплю, позвоните в один из следующих методов:

 - [СтартКопия](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

Следующий пример кода получает ссылку на каплю, созданную ранее, и копирует его на новую каплю в том же контейнере:

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

## <a name="abort-a-blob-copy-operation"></a>Прервать операцию копирования капли

Прерывание операции копирования приводит к пункту назначения нулевой длины для блоковых капли, капли приложения и капли страниц. Тем не менее, метаданные для капли назначения будут иметь новые значения, скопированные из исходной капли или установить явно в [вызове StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) или [StartCopyAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) Чтобы сохранить исходные метаданные до копирования, сделайте снимок `StartCopy` `StartCopyAsync`капли назначения перед вызовом или .

При прерывании текущей операции копирования капли, пункт назначения [Blob в CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) установлен на [CopyStatus.Aborted.](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet)

Методы [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) и [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) отменяют текущую операцию копирования капли и оставляют каплю назначения с нулевой длиной и полными метаданными.

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

Следующие темы содержат информацию о копировании капли и прерывании текущих операций копирования с помощью ApIs Azure REST.

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Прерывание копирования большого двоичного объекта](/rest/api/storageservices/abort-copy-blob)
