---
title: Управление доступом общественности к чтению контейнеров и капли
titleSuffix: Azure Storage
description: Узнайте, как сделать контейнеры и большие двоичные объекты доступными для анонимного доступа и как осуществлять к ним программный доступ.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4d9a54c220861b19d67b07998e609ee72897446a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255487"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Управление анонимным доступом на чтение к контейнерам и большим двоичным объектам

Можно включить анонимный открытый доступ на чтение к контейнеру и его большим двоичным объектам в хранилище BLOB-объектов Azure. Таким образом можно предоставить доступ только для чтения к этим ресурсам, не передавая ключ учетной записи и не требуя подписанного URL-адреса (SAS).

Открытый доступ на чтение лучше всего подходит для сценариев, когда определенные большие двоичные объекты нужно сделать всегда доступными для анонимного доступа на чтение. Для более точного контроля можно создать подписанный URL-адрес. Подписанные URL-адреса позволяют предоставить ограниченный доступ с различными разрешениями на определенный период времени. Дополнительные сведения о создании подписанных URL-адресов см. в статье [Использование подписанных URL-адресов (SAS)](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Предоставление анонимным пользователям разрешений для контейнеров и больших двоичных объектов

По умолчанию контейнер и любые большие двоичные объекты внутри него могут быть доступны только пользователю, которому были предоставлены соответствующие разрешения. Чтобы предоставить анонимным пользователям доступ на их чтение, следует разрешить открытый доступ к контейнеру. Когда вы предоставляете открытый доступ к контейнеру, анонимные пользователи могут считывать данные большого двоичного объекта из общедоступного контейнера, при этом их запросы не будут проходить авторизацию.

Можно настроить контейнер со следующими разрешениями:

- **Без открытого доступа на чтение.** К контейнеру и его большим двоичным объектам может обращаться только владелец учетной записи хранения. Это способ доступа по умолчанию для всех новых контейнеров.
- **Общий доступ на чтение только для больших двоичных объектов.** Большие двоичные объекты в контейнере можно считать с помощью анонимного запроса, но данные контейнера недоступны. Анонимные клиенты не могут перечислять большие двоичные объекты внутри контейнера.
- **Общественный доступ к чтению для контейнера и его капли:** Все данные контейнера и капли могут быть прочитаны по анонимному запросу. Клиенты могут перечислять большие двоичные объекты внутри контейнера с помощью анонимного запроса, но не могут перечислять контейнеры в учетной записи хранения.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Установите уровень публичного доступа контейнеров на портале Azure

С [портала Azure](https://portal.azure.com)можно обновить уровень общедоступного доступа для одного или нескольких контейнеров:

1. Перейдите к обзору учетной записи хранилища на портале Azure.
1. Под **blob службы** на лезвие меню, выберите **Blobs**.
1. Выберите контейнеры, для которых требуется установить уровень общедоступного доступа.
1. Используйте кнопку **уровня доступа Изменения** для отображения настроек общедоступного доступа.
1. Выберите желаемый уровень общедоступного доступа из уровня public **access** dropdown и нажмите кнопку OK, чтобы применить изменение к выбранным контейнерам.

На следующем скриншоте показано, как изменить уровень общего доступа для выбранных контейнеров.

![Скриншот, показывающий, как установить уровень общественного доступа на портале](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Вы не можете изменить уровень общественного доступа для отдельной капли. Уровень публичного доступа устанавливается только на уровне контейнера.

### <a name="set-container-public-access-level-with-net"></a>Установите уровень публичного доступа контейнера с помощью .NET

Чтобы установить разрешения для контейнера, используя клиентскую библиотеку Azure Storage для .NET, сначала извилите существующие разрешения контейнера, позвонив в один из следующих методов:

- [ПолучитьРазрешения](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Затем установите свойство **PublicAccess** на объект [blobContainerPermissions,](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) который возвращается методом **GetPermissions.**

Наконец, позвоните в один из следующих методов для обновления разрешений контейнера:

- [Наборы разрешений](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

В следующем примере задаются разрешения контейнера на полный общий доступ на чтение. Чтобы задать общий доступ на чтение только для BLOB-объектов, задайте для свойства **PublicAccess** значение **BlobContainerPublicAccessType.Blob**. Чтобы удалить все разрешения для анонимных пользователей, задайте для этого свойства значение **BlobContainerPublicAccessType.Off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Анонимный доступ к контейнерам и большим двоичным объектам

Клиент, который обращается к контейнерам и большим двоичным объектам анонимно, может использовать конструкторы, не требующие учетных данных. Следующие примеры показывают несколько различных способов ссылки контейнеров и капли анонимно.

### <a name="create-an-anonymous-client-object"></a>Создание анонимного клиентского объекта

Вы можете создать новый объект клиента службы для анонимного доступа, предоставив конечную точку хранения Blob для учетной записи. Тем не менее необходимо также знать имя контейнера в этой учетной записи, которая доступна для анонимного доступа.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Анонимная ссылка на контейнер

При наличии URL-адреса контейнера, который доступен анонимно, можно воспользоваться им для прямой ссылки на контейнер.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Анонимная ссылка на большой двоичный объект

Если имеется URL-адрес большого двоичного объекта, который доступен для анонимного доступа, можно создать прямую ссылку на этот большой двоичный объект, используя URL-адрес.

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>Дальнейшие действия

- [Авторизация доступа к службе хранилища Azure](../common/storage-auth.md)
- [Предоставляетограниченный доступ к ресурсам хранения Azure с помощью общих подписей доступа (SAS)](../common/storage-sas-overview.md)
- [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api) (API-интерфейс REST службы BLOB-объектов)
