---
title: Управление свойствами и метаданными для большого двоичного объекта с помощью .NET — служба хранилища Azure
description: Узнайте, как задавать и получать системные свойства, а также хранить пользовательские метаданные в больших двоичных объектах в учетной записи хранения Azure с помощью клиентской библиотеки .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 03171e395bb23da05ee5420f60abca9a16ffb774
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600297"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Управление свойствами и метаданными больших двоичных объектов с помощью .NET

В дополнение к содержащимся в них данным, большие двоичные объекты поддерживают системные свойства и определяемые пользователем метаданные. В этой статье показано, как управлять свойствами системы и определяемыми пользователем метаданными с помощью [клиентской библиотеки службы хранилища Azure для .NET](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>Сведения о свойствах и метаданных

- **Свойства системы**: свойства системы существуют в каждом ресурсе хранилища BLOB-объектов. Некоторые из них можно считать или задать, некоторые — только считать. На самом деле, некоторые свойства системы соответствуют определенным стандартным заголовкам HTTP. Клиентская библиотека службы хранилища Azure для .NET сохраняет эти свойства.

- **Определяемые пользователем метаданные**. определяемые пользователем метаданные состоят из одной или нескольких пар "имя-значение", указанных для ресурса хранилища BLOB-объектов. Метаданные можно использовать для хранения дополнительных значений в ресурсе. Значения метаданных предназначены только для собственных целей и не влияют на работу ресурса.

Получение метаданных и значений свойств для ресурса хранилища BLOB-объектов — это двухэтапный процесс. Прежде чем можно будет прочитать эти значения, необходимо явно получить их, вызвав метод `FetchAttributes` или `FetchAttributesAsync`. Исключением из этого правила является то, что методы `Exists` и `ExistsAsync` вызывают соответствующий метод `FetchAttributes`, как описано в разделе. При вызове одного из этих методов не требуется также вызывать `FetchAttributes`.

> [!IMPORTANT]
> Если вы обнаружите, что значения свойств или метаданных для ресурса хранилища не заполнены, убедитесь, что код вызывает метод `FetchAttributes` или `FetchAttributesAsync`.

## <a name="set-and-retrieve-properties"></a>Задание и получение свойств

В следующем примере кода задаются свойства системы `ContentType` и `ContentLanguage` большого двоичного объекта.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Чтобы получить свойства большого двоичного объекта, вызовите метод `FetchAttributes` или `FetchAttributesAsync` для большого двоичного объекта, чтобы заполнить свойство `Properties`. Следующий пример кода получает системные свойства большого двоичного объекта и отображает некоторые из значений:

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>Установка и получение метаданных

Метаданные можно указать как одну или несколько пар "имя-значение" для BLOB-ресурса или ресурса контейнера. Чтобы задать метаданные, добавьте пары "имя-значение" в `Metadata`ную коллекцию ресурса. Затем вызовите один из следующих методов для записи значений:

- [сетметадата](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [сетметадатаасинк](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Пары "имя-значение" метаданных являются допустимыми заголовками HTTP и должны соответствовать всем ограничениям, регулирующим заголовки HTTP. Имена метаданных должны быть допустимыми именами HTTP-заголовков и допустимыми C# идентификаторами, могут содержать только символы ASCII и должны обрабатываться без учета регистра. Кодировка [Base64](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) или [URL-кодирование](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) значений метаданных, содержащих символы, не входящие в набор ASCII.

Имя метаданных должно соответствовать соглашениям об именовании идентификаторов C#. Имена метаданных поддерживают регистр, используемый при создании, но не учитывают регистр при установке или чтении. Если для ресурса отправляется несколько заголовков метаданных с одним и тем же именем, хранилище BLOB-объектов Azure возвращает код ошибки HTTP 400 (недопустимый запрос).

В следующем примере кода задаются метаданные для большого двоичного объекта. Одно из значений задается с помощью метода `Add` коллекции. Другое значение задается с помощью неявного синтаксиса «ключ/значение».

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Чтобы получить метаданные, вызовите метод `FetchAttributes` или `FetchAttributesAsync` для большого двоичного объекта или контейнера, чтобы заполнить коллекцию `Metadata`, а затем считайте значения, как показано в примере ниже.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Дополнительные материалы

- [Операция задания свойств BLOB-объекта](/rest/api/storageservices/set-blob-properties)
- [Операция получения свойств BLOB-объекта](/rest/api/storageservices/get-blob-properties)
- [Операция задания метаданных BLOB-объекта](/rest/api/storageservices/set-blob-metadata)
- [Операция получения метаданных BLOB-объекта](/rest/api/storageservices/set-blob-metadata)
