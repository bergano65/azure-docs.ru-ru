---
title: Управление свойствами и метаданными для капли с помощью .NET - Azure Storage
description: Узнайте, как устанавливать и извлекать свойства системы и хранить пользовательские метаданные на каплях в учетной записи Хранилища Azure с помощью клиентской библиотеки .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137668"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Управление свойствами капли и метаданными с помощью .NET

В дополнение к данным, которые они содержат, капли поддержки системных свойств и пользовательских метаданных. В этой статье показано, как управлять системными свойствами и метаданными, определяемыми пользователем, с [помощью клиентской библиотеки Azure Storage для .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="about-properties-and-metadata"></a>О свойствах и метаданных

- **Свойства системы**: Свойства системы существуют на каждом ресурсе хранения Blob. Некоторые из них можно считать или задать, некоторые — только считать. На самом деле, некоторые свойства системы соответствуют определенным стандартным заголовкам HTTP. Клиентская библиотека Azure Storage для .NET поддерживает эти свойства для вас.

- **Метаданные**с определенным иным пользователем: Метаданные с определенным ими пользователя состоят из одной или нескольких пар имено-стоимости, которые вы указываете для ресурса хранения Blob. Метаданные можно использовать для хранения дополнительных значений с помощью ресурса. Значения метаданных предназначены только для ваших целей и не влияют на то, как ведет себя ресурс.

Извлечение метаданных и значений свойств для ресурса хранения Blob — это двухэтапный процесс. Прежде чем вы сможете прочитать эти значения, `FetchAttributes` вы `FetchAttributesAsync` должны явно принести их, позвонив или метод. Исключением из этого правила `Exists` `ExistsAsync` является то, `FetchAttributes` что методы называют соответствующий метод под крышкой. При вызове одного из этих методов вам не `FetchAttributes`нужно также вызывать.

> [!IMPORTANT]
> Если вы обнаружите, что значения свойств или метаданных для ресурса хранения `FetchAttributes` не `FetchAttributesAsync` были заселены, проверьте, вызывает ли ваш код метод или метод.

## <a name="set-and-retrieve-properties"></a>Установка и извлечение свойств

Следующий пример кода `ContentType` `ContentLanguage` устанавливает и системные свойства на каплей.

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

Чтобы получить свойства blob, `FetchAttributes` `FetchAttributesAsync` позвоните или метод на `Properties` кабро, чтобы заполнить свойство. Следующий пример кода получает свойства системы капли и отображает некоторые значения:

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

## <a name="set-and-retrieve-metadata"></a>Установка и извлечение метаданных

Метаданные можно указать как одну или несколько пар "имя-значение" для BLOB-ресурса или ресурса контейнера. Чтобы установить метаданные, добавьте пары `Metadata` имено-ценности в коллекцию на ресурсе. Затем позвоните в один из следующих методов для записи значений:

- [СетМетадата](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Пары имен/значений метаданных являются действительными заголовками HTTP и должны соблюдать все ограничения, регулирующие заголовки HTTP. Имена метаданных должны быть действительными именами заголовков HTTP и действительными идентификаторами C,s, могут содержать только символы ASCII и должны рассматриваться как нечувствительные к случаям. Значения метаданных [Base64-кодируют](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) или [КОДируют URL-коды,](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) содержащие не-ASCII символы.

Имя метаданных должно соответствовать соглашениям об именовании идентификаторов C#. Имена метаданных сохраняют случай, используемый при их создании, но нечувствительны при установке или чтении. Если для ресурса будут отправлены два или более заголовков метаданных с использованием одного и того же имени, хранилище Azure Blob возвращает код ошибки HTTP 400 (Bad Request).

Следующий пример кода устанавливает метаданные на каплей. Одно значение устанавливается с `Add` использованием метода коллекции. Другое значение задается с помощью неявного синтаксиса «ключ/значение».

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

Чтобы получить метаданные, `FetchAttributes` позвоните или `FetchAttributesAsync` метод на каплю `Metadata` или контейнер для заполнения коллекции, а затем прочитайте значения, как показано в примере ниже.

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

## <a name="see-also"></a>См. также

- [Установка Blob Свойства операции](/rest/api/storageservices/set-blob-properties)
- [Получить Blob Свойства операции](/rest/api/storageservices/get-blob-properties)
- [Установка операции Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Получить Blob Метаданные операции](/rest/api/storageservices/get-blob-metadata)
