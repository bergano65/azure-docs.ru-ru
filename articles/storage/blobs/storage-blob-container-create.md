---
title: Создание или удаление контейнера с blob с помощью .NET - Azure Storage
description: Узнайте, как создать или удалить контейнер с каплей в учетной записи Хранилища Azure с помощью клиентской библиотеки .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135944"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Создание или удаление контейнера в хранилище Azure с помощью .NET

Blobs в Azure Storage организуются в контейнеры. Прежде чем вы сможете загрузить каплю, вы должны сначала создать контейнер. В этой статье показано, как создавать и удалять контейнеры с [библиотекой клиентов Azure Storage для .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="name-a-container"></a>Назовите контейнер

Имя контейнера должно быть действительным именем DNS, так как оно является частью уникального URI, используемого для решения контейнера или его капли. Следуйте этим правилам при наименовании контейнера:

- Имена контейнеров могут быть от 3 до 63 символов в длину.
- Имена контейнеров должны начинаться с буквы или номера и могут содержать только буквы, цифры и символ тире (-)
- Два или более последовательных символов тире не допускаются в названиях контейнеров.

URI для контейнера находится в этом формате:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Создание контейнера

Чтобы создать контейнер, позвоните в один из следующих методов:

- [Создать](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateifnotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Методы **Create** and **CreateAsync** создают исключение, если контейнер с тем же именем уже существует.

**Методы CreateIfNotExists** и **CreateIfNotExistsAsync** возвращают значение Boolean, указывающее, был ли создан контейнер. Если контейнер с тем же именем уже существует, то эти методы **возвращаются ложными,** чтобы указать, что новый контейнер не был создан.

Контейнеры создаются для учетной записи хранилища немедленно. Нельзя вложить один контейнер в другой.

Следующий пример создает контейнер асинхронно:

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>Создание корневого контейнера

Корневой контейнер является контейнером по умолчанию для вашей учетной записи хранилища. Каждая учетная запись хранилища может иметь один корневой контейнер, который должен быть назван *$root.*. Необходимо явно создать или удалить корневой контейнер.

Вы можете ссылаться на каплю, хранящуюся в корневом контейнере, не включая имя корневого контейнера. Корневой контейнер позволяет ссылаться на каплю на верхнем уровне иерархии учетной записи хранилища. Например, теперь вы можете обратиться к BLOB-объекту, который находится в корневом контейнере, следующим образом.

`https://myaccount.blob.core.windows.net/default.html`

Следующий пример создает корневой контейнер синхронно:

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>Удаление контейнера

Чтобы удалить контейнер в .NET, используйте один из следующих методов:

- [Удалить](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Методы **Удаления** и **удаленияAsync** выдают исключение, если контейнер не существует.

Методы **DeleteIfExists** и **DeleteIfExistsAsync** возвращают значение Boolean, указывающее, был ли контейнер удален. Если указанный контейнер не существует, то эти методы возвращают **сяпотку,** чтобы указать, что контейнер не был удален.

После удаления контейнера невозможно создать контейнер с тем же именем в течение по крайней мере 30 секунд и, возможно, дольше. Во время удаления контейнера попытка создания контейнера с тем же именем завершится неудачей с кодом ошибки HTTP 409 (Конфликт). Любые другие операции на контейнере или капли, которые он содержит, не сдадутся с кодом ошибки HTTP 404 (не найдено) во время удаления контейнера.

Следующий пример удаляет указанный контейнер и обрабатывает исключение, если контейнер не существует:

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
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

В следующем примере показано, как удалить все контейнеры, которые начинаются с указанной префикса. Пример нарушает договор аренды при наличии существующей аренды контейнера.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>См. также

- [Создание операции контейнера](/rest/api/storageservices/create-container)
- [Операция удаления контейнера](/rest/api/storageservices/delete-container)
