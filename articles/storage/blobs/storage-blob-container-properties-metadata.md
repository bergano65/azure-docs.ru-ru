---
title: Используйте .NET для управления свойствами и метаданными для контейнера с каплями
titleSuffix: Azure Storage
description: Узнайте, как устанавливать и извлекать свойства системы и хранить пользовательские метаданные на контейнерах с каплями в вашей учетной записи хранения Azure с помощью клиентской библиотеки .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135927"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Управление контейнерными свойствами и метаданными с помощью .NET

Контейнеры Blob поддерживают свойства системы и метаданные, определяемые пользователем, в дополнение к содержащимся в них данным. В этой статье показано, как управлять системными свойствами и метаданными, определяемыми пользователем, с [помощью клиентской библиотеки Azure Storage для .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="about-properties-and-metadata"></a>О свойствах и метаданных

- **Свойства системы**: Свойства системы существуют на каждом ресурсе хранения Blob. Некоторые из них можно считать или задать, некоторые — только считать. На самом деле, некоторые свойства системы соответствуют определенным стандартным заголовкам HTTP. Клиентская библиотека Azure Storage для .NET поддерживает эти свойства для вас.

- **Метаданные**с определенным иным пользователем: Метаданные с определенным ими пользователя состоят из одной или нескольких пар имено-стоимости, которые вы указываете для ресурса хранения Blob. Метаданные можно использовать для хранения дополнительных значений с помощью ресурса. Значения метаданных предназначены только для ваших собственных целей и не влияют на поведение ресурса.

Извлечение значений свойств и метаданных для ресурса хранения Blob — это двухэтапный процесс. Прежде чем считывать эти значения, необходимо их четко получить, вызвав метод **FetchAttributes** или **FetchAttributesAsync**. Исключением из этого правила является то, что методы **«Существующие** и **существующиеАсинки»** вызывают под крышкой соответствующий метод **FetchAttributes.** При вызове одного из этих методов вам не нужно также вызывать **FetchAttributes.**

> [!IMPORTANT]
> Если обнаружится, что значения свойств или метаданных для ресурса хранилища не были заполнены, проверьте вызывает ли ваш код метод **FetchAttributes** или **FetchAttributesAsync**.

Пары имен/значений метаданных являются действительными заголовками HTTP, и поэтому должны соблюдать все ограничения, регулирующие заголовки HTTP. Имена метаданных должны быть действительными именами заголовков HTTP и действительными идентификаторами C,s, могут содержать только символы ASCII и должны рассматриваться как нечувствительные к случаям. Значения метаданных, содержащие символы, не относясь к ASCII, должны быть закодированы или закодированы URL-адресами.

## <a name="retrieve-container-properties"></a>Извлечение свойств контейнера

Чтобы получить свойства контейнера, позвоните в один из следующих методов:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

Следующий пример кода приводит к свойствам системы контейнера и записывает некоторые значения свойств в окно консоли:

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

## <a name="set-and-retrieve-metadata"></a>Установка и извлечение метаданных

Метаданные можно указать как одну или несколько пар "имя-значение" для BLOB-ресурса или ресурса контейнера. Чтобы установить метаданные, добавьте пары именных значений в коллекцию **Метаданных** на ресурсе, а затем позвоните в один из следующих методов для записи значений:

- [СетМетадата](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Имя метаданных должно соответствовать соглашениям об именовании идентификаторов C#. Имена метаданных сохраняют случай, с которым они были созданы, но нечувствительны при установке или чтении. Если для ресурса будут отправлены два или более заголовков метаданных с тем же именем, хранилище Blob возвращает код ошибки HTTP 400 (Bad Request).

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>См. также

- [Получить контейнер Свойства операции](/rest/api/storageservices/get-container-properties)
- [Установка операции метаданных контейнеров](/rest/api/storageservices/set-container-metadata)
- [Получить контейнер Метаданные операции](/rest/api/storageservices/set-container-metadata)
