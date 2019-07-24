---
title: Управление свойствами и метаданными для контейнера больших двоичных объектов с помощью .NET в службе хранилища Azure
description: Узнайте, как задавать и получать системные свойства, а также хранить пользовательские метаданные в контейнерах больших двоичных объектов в учетной записи хранения Azure с помощью клиентской библиотеки .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.openlocfilehash: bd745c54fb659729b1e31f9975b2e4e0b6a97a83
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235253"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Управление свойствами контейнера и метаданными с помощью .NET

Контейнеры больших двоичных объектов поддерживают системные свойства и определяемые пользователем метаданные в дополнение к содержащимся в них данным. В этой статье показано, как управлять свойствами системы и определяемыми пользователем метаданными с помощью [клиентской библиотеки службы хранилища Azure для .NET](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>Сведения о свойствах и метаданных

- **Свойства системы**: Для каждого ресурса хранилища BLOB-объектов существуют системные свойства. Некоторые из них можно считать или задать, некоторые — только считать. На самом деле, некоторые свойства системы соответствуют определенным стандартным заголовкам HTTP. Клиентская библиотека службы хранилища Azure для .NET сохраняет эти свойства.

- **Определяемые пользователем метаданные**: Определяемые пользователем метаданные состоят из одной или нескольких пар "имя-значение", указанных для ресурса хранилища BLOB-объектов. Метаданные можно использовать для хранения дополнительных значений в ресурсе. Значения метаданных предназначены только для ваших собственных целей и не влияют на поведение ресурса.

Получение значений свойств и метаданных для ресурса хранилища BLOB-объектов — это двухэтапный процесс. Прежде чем считывать эти значения, необходимо их четко получить, вызвав метод **FetchAttributes** или **FetchAttributesAsync**. Исключением из этого правила является то,  что методы Exists и **ексистсасинк** вызывают соответствующий метод **FetchAttributes** , как описано в разделе. При вызове одного из этих методов не требуется также вызывать **FetchAttributes**.

> [!IMPORTANT]
> Если обнаружится, что значения свойств или метаданных для ресурса хранилища не были заполнены, проверьте вызывает ли ваш код метод **FetchAttributes** или **FetchAttributesAsync**.

Пары "имя-значение" метаданных являются допустимыми заголовками HTTP и должны соответствовать всем ограничениям, регулирующим заголовки HTTP. Имена метаданных должны быть допустимыми именами HTTP-заголовков и допустимыми C# идентификаторами, могут содержать только символы ASCII и должны обрабатываться без учета регистра. Значения метаданных, содержащие символы, отличные от ASCII, должны быть в кодировке Base64 или в кодировке URL.

## <a name="retrieve-container-properties"></a>Получение свойств контейнера

Чтобы получить свойства контейнера, вызовите один из следующих методов:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

Следующий пример кода извлекает системные свойства контейнера и записывает некоторые значения свойств в окно консоли:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

Метаданные можно указать как одну или несколько пар "имя-значение" для BLOB-ресурса или ресурса контейнера. Чтобы задать метаданные, добавьте пары "имя-значение" в коллекцию **метаданных** ресурса, а затем вызовите один из следующих методов для записи значений:

- [сетметадата](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [сетметадатаасинк](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Имя метаданных должно соответствовать соглашениям об именовании идентификаторов C#. Имена метаданных сохраняют регистр, с которым они были созданы, но не учитывают регистр при установке или чтении. Если для ресурса отправляется несколько заголовков метаданных с одним и тем же именем, хранилище BLOB-объектов возвращает код ошибки HTTP 400 (недопустимый запрос).

В следующем примере кода задаются метаданные для контейнера. Одно значение задается с помощью метода коллекции **Add** . Другое значение задается с помощью неявного синтаксиса «ключ/значение». Можно использовать любой из способов.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

Чтобы получить метаданные, вызовите метод **FetchAttributes** или **FetchAttributesAsync** для BLOB-объекта или контейнера, чтобы заполнить коллекцию **Metadata**, а затем считайте значения, как показано в примере ниже.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
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

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="see-also"></a>См. также

- [Операция получения свойств контейнера](/rest/api/storageservices/get-container-properties)
- [Операция задания метаданных контейнера](/rest/api/storageservices/set-container-metadata)
- [Операция получения метаданных контейнера](/rest/api/storageservices/set-container-metadata)
