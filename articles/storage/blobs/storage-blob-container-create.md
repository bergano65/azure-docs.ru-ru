---
title: Создание или удаление контейнера больших двоичных объектов с помощью .NET в службе хранилища Azure
description: Узнайте, как создать или удалить контейнер больших двоичных объектов в учетной записи хранения Azure с помощью клиентской библиотеки .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: d1218b10eadf0788752bab2aec4b21614666888c
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671276"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Создание или удаление контейнера в службе хранилища Azure с помощью .NET

Большие двоичные объекты в службе хранилища Azure организованы в контейнеры. Перед отправкой большого двоичного объекта необходимо сначала создать контейнер. В этой статье показано, как создавать и удалять контейнеры с помощью [клиентской библиотеки службы хранилища Azure для .NET](/dotnet/api/overview/azure/storage/client).

## <a name="name-a-container"></a>Имя контейнера

Имя контейнера должно быть допустимым DNS-именем, так как оно образует часть уникального URI, используемого для адресации контейнера или его больших двоичных объектов. При именовании контейнера следует соблюдать следующие правила.

- Длина имени контейнера может составлять от 3 до 63 символов.
- Имена контейнеров должны начинаться с буквы или цифры и могут содержать только строчные буквы, цифры и знак тире (-).
- В именах контейнеров не допускаются два или более последовательных дефисов.

Универсальный код ресурса (URI) для контейнера имеет следующий формат:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Создать контейнер

Чтобы создать контейнер, вызовите один из следующих методов.

- [Создание](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Методы **CREATE** и **CreateAsync** вызовут исключение, если контейнер с таким именем уже существует.

Методы **CreateIfNotExists** и **CreateIfNotExistsAsync** возвращают логическое значение, указывающее, был ли создан контейнер. Если контейнер с таким именем уже существует, эти методы возвращают **значение false** , чтобы указать, что новый контейнер не был создан.

Контейнеры создаются непосредственно под учетной записью хранения. Невозможно вложить один контейнер ниже другого.

В следующем примере контейнер создается асинхронно:

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

Корневой контейнер служит контейнером по умолчанию для вашей учетной записи хранения. Каждая учетная запись хранения может иметь один корневой контейнер, который должен иметь имя *$root.* . Необходимо явно создать или удалить корневой контейнер.

Вы можете ссылаться на большой двоичный объект, хранящийся в корневом контейнере, без включения имени корневого контейнера. Корневой контейнер позволяет сослаться на большой двоичный объект на верхнем уровне иерархии учетной записи хранения. Например, можно сослаться на большой двоичный объект, который находится в корневом контейнере, следующим образом:

`https://myaccount.blob.core.windows.net/default.html`

В следующем примере корневой контейнер создается синхронно:

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

Чтобы удалить контейнер в .NET, используйте один из следующих методов.

- [Удаление](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Методы **Delete** и **DeleteAsync** вызовут исключение, если контейнер не существует.

Методы **делетеифнотексистс** и **Делетеифнотексистсасинк** возвращают логическое значение, указывающее, был ли удален контейнер. Если указанный контейнер не существует, эти методы возвращают **значение false** , чтобы указать, что контейнер не был удален.

После удаления контейнера нельзя создать контейнер с тем же именем в течение не менее 30 секунд и, возможно, дольше. Во время удаления контейнера попытка создать контейнер с тем же именем завершится с кодом ошибки HTTP 409 (конфликт). Любые другие операции с контейнером или BLOB-объектами, которые он содержит, завершатся ошибкой HTTP с кодом 404 (не найдено) во время удаления контейнера.

В следующем примере удаляется указанный контейнер и обрабатывается исключение, если контейнер не существует:

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

В следующем примере показано, как удалить все контейнеры, начинающиеся с указанного префикса. Этот пример прерывает аренду, если в контейнере есть аренда.

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

- [Операция создания контейнера](/rest/api/storageservices/create-container)
- [Операция удаления контейнера](/rest/api/storageservices/delete-container)
