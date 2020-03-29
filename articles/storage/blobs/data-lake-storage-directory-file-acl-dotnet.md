---
title: Лазурное хранилище озер Данных Gen2 .NET SDK для файлов & AcLs
description: Используйте клиентскую библиотеку Azure Storage для управления каталогами и списками управления доступом файлов и каталогов (ACL) в учетных записях хранения с включенным иерархическим пространством имен (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b83d0d2d765b60585832f1a3e7c610f05eac075c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061581"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Используйте .NET для управления каталогами, файлами и ACL в Azure Data Lake Storage 2

В этой статье показано, как использовать .NET для создания и управления каталогами, файлами и разрешениями в учетных записях хранения с включенным иерархическим пространством имен (HNS). 

[Пакет (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Образцы](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API ссылка](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Gen1 на Gen2 отображение](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | Дать[обратную связь](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранилища с включенным иерархическим пространством имен (HNS). Следуйте [этим](data-lake-storage-quickstart-create-account.md) инструкциям, чтобы создать один.

## <a name="set-up-your-project"></a>Настройка проекта

Для начала установите пакет [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet.

Для получения дополнительной информации о том, как установить пакеты NuGet, [см.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)

Затем добавьте их с помощью инструкций в верхнюю часть файла кода.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Подключение к учетной записи

Чтобы использовать фрагменты в этой статье, необходимо создать экземпляр [DataLakeServiceClient,](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) представляющий учетную запись хранилища. 

### <a name="connect-by-using-an-account-key"></a>Подключение с помощью ключа учетной записи

Это самый простой способ подключения к учетной записи. 

Этот пример создает экземпляр [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) с помощью ключа учетной записи.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

### <a name="connect-by-using-azure-active-directory-ad"></a>Подключение с помощью активного каталога Azure (AD)

Для проверки подлинности приложения с помощью Azure AD можно использовать [библиотеку клиентов-идентификационных данных Azure.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)

Этот пример создает экземпляр [DataLakeServiceClient,](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) используя идентификатор клиента, секрет клиента и идентификатор клиента.  Чтобы получить эти значения, [см. Приобрести токен из Azure AD для авторизации запросов из клиентского приложения.](../common/storage-auth-aad-app.md)

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> Для получения дополнительных примеров можно найти [в библиотеке клиентов итики Azure документацию .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система действует как контейнер для файлов. Вы можете создать его, позвонив по методу [DataLakeServiceClient.CreateFileSystem.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync)

Этот пример создает файловую систему под названием `my-file-system`. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на каталог, позвонив по методу [DataLakeFileSystemClient.CreateDirectoryAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync)

Этот пример добавляет каталог `my-directory` с именем в файловую систему, `my-subdirectory`а затем добавляет подкаталог с именем . 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуй или переместите каталог, позвонив в метод [DataLakeDirectoryClient.RenameAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) Передайте путь желаемого каталога параметру. 

Этот пример переименовывает подкаталог `my-subdirectory-renamed`к имени .

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Этот пример перемещает каталог `my-subdirectory-renamed` с именем в подкаталог `my-directory-2`каталог аменем . 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалить каталог, позвонив в [метод DataLakeDirectoryClient.Delete.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete)

Этот пример удаляет каталог `my-directory`с именем .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Управление каталогом ACL

Получить список управления доступом (ACL) каталога, позвонив [dataLakeDirectoryClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) метод и установить ACL, позвонив [DataLakeDirectoryClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) метод.

> [!NOTE]
> Если приложение разрешает доступ с помощью Active Directory Azure (Azure AD), убедитесь, что принципбезопасности, который приложение использует для авторизации доступа, был назначен [роль владельца данных Storage Blob.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Чтобы узнать больше о том, как применяются разрешения [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ACL и о последствиях их изменения, см. 

Этот пример получает и устанавливает ACL `my-directory`каталога с именем . Строка `user::rwx,group::r-x,other::rw-` дает пользователю-владельцеу читать, писать и выполнять разрешения, дает группе владельцев только читать и выполнять разрешения, а также дает всем остальным разрешение на чтение и запись.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>Загрузить файл в каталог

Во-первых, создайте ссылку на файл в целевом каталоге, создав экземпляр класса [DataLakeFileClient.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) Загрузите файл, позвонив по методу [DataLakeFileClient.AppendAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) Убедитесь в том, чтобы завершить загрузку, позвонив [в DataLakeFileClient.FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) метод.

Этот пример загружает текстовый `my-directory`файл в каталог с именем .    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

> [!TIP]
> Если размер файла большой, ваш код должен будет сделать несколько звонков на [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Вместо этого рассмотрим метод [DataLakeFileClient.UploadAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) Таким образом, вы можете загрузить весь файл в один вызов. 
>
> Пример см. в следующем разделе.

## <a name="upload-a-large-file-to-a-directory"></a>Загрузить большой файл в каталог

Используйте метод [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) для загрузки больших файлов без необходимости совершать несколько звонков на метод [DataLakeFileClient.AppendAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="manage-a-file-acl"></a>Управление файлом ACL

Получить список управления доступом (ACL) файла, позвонив [dataLakeFileClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) метод и установить ACL, позвонив [dataLakeFileClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) метод.

> [!NOTE]
> Если приложение разрешает доступ с помощью Active Directory Azure (Azure AD), убедитесь, что принципбезопасности, который приложение использует для авторизации доступа, был назначен [роль владельца данных Storage Blob.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Чтобы узнать больше о том, как применяются разрешения [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ACL и о последствиях их изменения, см. 

Этот пример получает и устанавливает ACL `my-file.txt`файла с именем. Строка `user::rwx,group::r-x,other::rw-` дает пользователю-владельцеу читать, писать и выполнять разрешения, дает группе владельцев только читать и выполнять разрешения, а также дает всем остальным разрешение на чтение и запись.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Скачать из каталога 

Во-первых, создайте экземпляр [DataLakeFileClient,](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) представляющий файл, который вы хотите загрузить. Используйте метод [DataLakeFileClient.ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) и разбирайте значение возврата для получения объекта [Stream.](https://docs.microsoft.com/dotnet/api/system.io.stream) Используйте aPI обработки файлов .NET, чтобы сохранить байты из потока в файл. 

Этот пример использует [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) и [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) для сохранения байтов в файле. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Перечислите содержимое каталога, позвонив в метод [FileSystemClient.GetPathsAsync,](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) а затем перечислив результаты.

Этот пример печатает имена каждого файла, который находится `my-directory`в каталоге с именем .

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>См. также

* [Справочная документация по API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Пакет (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Примеры](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Отображение Gen1 до Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Дайте обратную связь](https://github.com/Azure/azure-sdk-for-net/issues)

