---
title: Задание и получение свойств и метаданных объектов в службе хранилища Azure | Документация Майкрософт
description: Хранение пользовательских метаданных для объектов в службе хранилища Azure, а также задание и получение свойств системы.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.openlocfilehash: e85cfb6d7b44924ce7f17fdedb7f1b52350ab598
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392404"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Задание и получение свойств и метаданных

Кроме данных, которые они содержат, объекты в службе хранилища Azure поддерживают свойства системы и пользовательские метаданные. В этой статье описывается управление свойствами системы и определяемыми пользователем метаданными с помощью [клиентской библиотеки хранилища Azure для .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Системные свойства**:  Свойства системы есть у каждого ресурса хранилища. Некоторые из них можно считать или задать, некоторые — только считать. На самом деле, некоторые свойства системы соответствуют определенным стандартным заголовкам HTTP. Эти свойства хранятся в клиентских библиотеках службы хранилища Azure.

* **Определяемые пользователем метаданные**: Определяемые пользователем метаданные состоит из одного или нескольких пар имя значение, указанные для ресурса службы хранилища Azure. Вы можете использовать метаданные для хранения дополнительных значений с помощью ресурса хранилища. Значения метаданных предназначены только для ваших собственных целей и не влияют на поведение ресурса.

Получение значений свойств и метаданных ресурса хранилища выполняется в два этапа. Прежде чем считывать эти значения, необходимо их четко получить, вызвав метод **FetchAttributes** или **FetchAttributesAsync**. Кроме случая с вызовом на ресурсе методов **Exists** или **ExistsAsync**. При вызове одного из этих методов служба хранилища Azure вызывает соответствующий метод **FetchAttributes** как часть вызова метода **Exists**.

> [!IMPORTANT]
> Если обнаружится, что значения свойств или метаданных для ресурса хранилища не были заполнены, проверьте вызывает ли ваш код метод **FetchAttributes** или **FetchAttributesAsync**.
>
> Пары имя/значение метаданных являются допустимыми заголовками HTTP и поэтому должен соответствовать всем ограничениям, относящихся к заголовкам HTTP. Имена метаданных должны быть допустимыми именами заголовка HTTP и допустимым C# идентификаторов, может содержать только символы ASCII и должны обрабатываться без учета регистра. Значения метаданных, содержащих отличные от ASCII символы должны быть Base64 или URL-адреса.

## <a name="setting-and-retrieving-properties"></a>Установка и получение свойств
Чтобы получить значения свойств, вызовите метод **FetchAttributesAsync** для BLOB-объекта или контейнера, а затем считайте значения.

Чтобы задать свойства объекта, укажите значение свойства, а затем вызовите метод **SetProperties** .

В следующем примере кода будет создан контейнер, а значения некоторых свойств будут выведены в окно консоли.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Установка и получение метаданных
Метаданные можно указать как одну или несколько пар "имя-значение" для BLOB-ресурса или ресурса контейнера. Чтобы задать метаданные, добавьте пары "имя — значение" в коллекцию **Metadata** ресурса, а затем вызовите метод **SetMetadata** или **SetMetadataAsync**, чтобы сохранить значения в службе.

> [!NOTE]
> Имя метаданных должно соответствовать соглашениям об именовании идентификаторов C#.
>
>

В следующем примере кода задаются метаданные для контейнера. Одно значение задается с помощью метода коллекции **Add** . Другое значение задается с помощью неявного синтаксиса «ключ/значение». Можно использовать любой из способов.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Чтобы получить метаданные, вызовите метод **FetchAttributes** или **FetchAttributesAsync** для BLOB-объекта или контейнера, чтобы заполнить коллекцию **Metadata**, а затем считайте значения, как показано в примере ниже.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
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
```

## <a name="next-steps"></a>Дальнейшие действия
* [Справочные материалы клиентской библиотеки хранилища Azure для .NET](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Клиентская библиотека хранилища Azure для пакетов NuGet .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
