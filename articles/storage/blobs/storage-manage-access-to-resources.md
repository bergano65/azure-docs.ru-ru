---
title: Включение открытого доступа на чтение к контейнерам и большим двоичным объектам в хранилище BLOB-объектов Azure | Документация Майкрософт
description: Узнайте, как сделать контейнеры и большие двоичные объекты доступными для анонимного доступа и как осуществлять к ним программный доступ.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: d0e3121fe773a9725eb7cfd9e8b14d0ed86f3fbb
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673298"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Управление анонимным доступом на чтение к контейнерам и большим двоичным объектам

Можно включить анонимный открытый доступ на чтение к контейнеру и его большим двоичным объектам в хранилище BLOB-объектов Azure. Таким образом можно предоставить доступ только для чтения к этим ресурсам, не передавая ключ учетной записи и не требуя подписанного URL-адреса (SAS).

Открытый доступ на чтение лучше всего подходит для сценариев, когда определенные большие двоичные объекты нужно сделать всегда доступными для анонимного доступа на чтение. Для более точного контроля можно создать подписанный URL-адрес. Подписанные URL-адреса позволяют предоставить ограниченный доступ с различными разрешениями на определенный период времени. Дополнительные сведения о создании подписанных URL-адресов см. в статье [Использование подписанных URL-адресов (SAS)](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Предоставление анонимным пользователям разрешений для контейнеров и больших двоичных объектов

По умолчанию контейнер и любые большие двоичные объекты внутри него могут быть доступны только пользователю, которому были предоставлены соответствующие разрешения. Чтобы предоставить анонимным пользователям доступ на чтение для контейнера и его больших двоичных объектов, можно задать общий уровень доступа контейнера. При предоставлении общего доступа к контейнеру анонимные пользователи могут считывать большие двоичные объекты в общедоступном контейнере без авторизации запроса.

Можно настроить контейнер со следующими разрешениями:

- **Нет общего доступа для чтения:** Доступ к контейнеру и его BLOB-объектам может осуществлять только владелец учетной записи хранения. Это способ доступа по умолчанию для всех новых контейнеров.
- **Общий доступ на чтение только для больших двоичных объектов:** Большие двоичные объекты в контейнере могут считываться анонимным запросом, но данные контейнера недоступны. Анонимные клиенты не могут перечислять большие двоичные объекты внутри контейнера.
- **Общий доступ на чтение для контейнера и его больших двоичных объектов:** Все данные контейнера и BLOB-объекта могут быть прочитаны анонимным запросом. Клиенты могут перечислять большие двоичные объекты внутри контейнера с помощью анонимного запроса, но не могут перечислять контейнеры в учетной записи хранения.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Задание уровня общего доступа к контейнеру в портал Azure

На [портал Azure](https://portal.azure.com)можно обновить общий уровень доступа для одного или нескольких контейнеров:

1. Перейдите к обзору учетной записи хранения в портал Azure.
1. В разделе **Служба BLOB-объектов** в колонке меню выберите **большие двоичные объекты**.
1. Выберите контейнеры, для которых необходимо задать общий уровень доступа.
1. Используйте кнопку **изменить уровень доступа** , чтобы отобразить параметры общего доступа.
1. Выберите нужный уровень общего доступа в раскрывающемся списке **общедоступный уровень доступа** и нажмите кнопку ОК, чтобы применить изменения к выбранным контейнерам.

На следующем снимке экрана показано, как изменить общий уровень доступа для выбранных контейнеров.

![Снимок экрана, показывающий, как задать общий уровень доступа на портале](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Нельзя изменить общий уровень доступа для отдельного большого двоичного объекта. Уровень общего доступа задается только на уровне контейнера.

### <a name="set-container-public-access-level-with-net"></a>Задание уровня общего доступа к контейнеру с помощью .NET

Чтобы задать разрешения для контейнера с помощью клиентской библиотеки службы хранилища Azure для .NET, сначала извлеките существующие разрешения контейнера, вызвав один из следующих методов:

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [жетпермиссионсасинк](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Затем задайте свойство **publicAccess** объекта [блобконтаинерпермиссионс](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) , возвращаемого **методом GetNext** .

Наконец, вызовите один из следующих методов, чтобы обновить разрешения контейнера:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
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

Клиент, который обращается к контейнерам и большим двоичным объектам анонимно, может использовать конструкторы, не требующие учетных данных. В следующих примерах показаны несколько различных способов анонимного обращения к контейнерам и BLOB-объектам.

### <a name="create-an-anonymous-client-object"></a>Создание анонимного клиентского объекта

Вы можете создать новый объект клиента службы для анонимного доступа, предоставив конечную точку хранилища BLOB-объектов для учетной записи. Тем не менее необходимо также знать имя контейнера в этой учетной записи, которая доступна для анонимного доступа.

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

## <a name="next-steps"></a>Следующие шаги

- [Авторизация доступа к службе хранилища Azure](../common/storage-auth.md)
- [Предоставление ограниченного доступа к ресурсам службы хранилища Azure с помощью подписанных URL-адресов (SAS)](../common/storage-sas-overview.md)
- [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api) (API-интерфейс REST службы BLOB-объектов)
