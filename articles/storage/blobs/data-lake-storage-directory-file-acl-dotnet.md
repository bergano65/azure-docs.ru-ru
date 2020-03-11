---
title: Azure Data Lake Storage 2-го поколения пакета SDK для .NET для файлов & ACL (Предварительная версия)
description: Используйте клиентскую библиотеку службы хранилища Azure для управления каталогами и списками управления доступом к файлам и каталогам (ACL) в учетных записях хранения с включенным иерархическое пространством имен (HNS).
author: normesta
ms.service: storage
ms.date: 01/09/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 0c279118df3a9205e82f8444b261922c688578da
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969083"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Использование .NET для управления каталогами, файлами и списками ACL в Azure Data Lake Storage 2-го поколения (Предварительная версия)

В этой статье показано, как использовать .NET для создания каталогов, файлов и разрешений в учетных записях хранения с включенным иерархическое пространством имен (HNS) и управления ими. 

> [!IMPORTANT]
> Пакет [Azure. Storage. Files. Data Lake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) NuGet в этой статье сейчас находится в общедоступной предварительной версии.

[Пакеты (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [примеры](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [справочника по API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Gen1 Gen2 сопоставления](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Отправить отзыв](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранения, в которой включено иерархическое пространство имен (HNS). Чтобы создать его [, выполните следующие](data-lake-storage-quickstart-create-account.md) инструкции.

## <a name="set-up-your-project"></a>Настройка проекта

Чтобы приступить к работе, установите пакет NuGet [Azure. Storage. Files. Data Lake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

Дополнительные сведения об установке пакетов NuGet см. в разделе [Установка пакетов и управление ими в Visual Studio с помощью диспетчера пакетов NuGet](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Затем добавьте эти операторы using в начало файла кода.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Подключение к учетной записи

Чтобы использовать фрагменты кода в этой статье, необходимо создать экземпляр [даталакесервицеклиент](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) , представляющий учетную запись хранения. Самый простой способ получить его — использовать ключ учетной записи. 

В этом примере создается экземпляр [даталакесервицеклиент](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) с помощью ключа учетной записи.

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

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система выступает в качестве контейнера для файлов. Его можно создать, вызвав метод [даталакесервицеклиент. креатефилесистем](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) .

В этом примере создается файловая система с именем `my-file-system`. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на каталог, вызвав метод [даталакефилесистемклиент. креатедиректорясинк](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) .

В этом примере в файловую систему добавляется каталог с именем `my-directory`, после чего добавляется подкаталог с именем `my-subdirectory`. 

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

Переименуйте или переместите каталог, вызвав метод [даталакедиректориклиент. ренамеасинк](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) . Передайте путь к нужному каталогу параметру. 

В этом примере подкаталог переименовывается в имя `my-subdirectory-renamed`.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

В этом примере каталог с именем `my-subdirectory-renamed` перемещается в подкаталог каталога с именем `my-directory-2`. 

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

Удалите каталог, вызвав метод [даталакедиректориклиент. Delete](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) .

В этом примере удаляется каталог с именем `my-directory`.  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Управление ACL каталога

Получите список управления доступом (ACL) каталога, вызвав метод [даталакедиректориклиент. жетакцессконтроласинк](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) и ЗАДАВ список ACL, вызвав метод [даталакедиректориклиент. сетакцессконтроллист](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

> [!NOTE]
> Если приложение разрешает доступ с помощью Azure Active Directory (Azure AD), убедитесь, что участнику безопасности, используемому приложением для авторизации доступа, назначена [роль владельца данных BLOB-объекта хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и их влиянии на их изменение см. в разделе [Контроль доступа в Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

В этом примере получается и задается список ACL для каталога с именем `my-directory`. Строка `user::rwx,group::r-x,other::rw-` предоставляет владельцам разрешения на чтение, запись и выполнение, предоставляет группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным разрешение на чтение и запись.

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

## <a name="upload-a-file-to-a-directory"></a>Передача файла в каталог

Сначала создайте ссылку на файл в целевом каталоге, создав экземпляр класса [даталакефилеклиент](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) . Отправьте файл, вызвав метод [даталакефилеклиент. аппендасинк](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) . Обязательно завершите передачу, вызвав метод [даталакефилеклиент. FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) .

В этом примере текстовый файл перегружается в каталог с именем `my-directory`.    

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

## <a name="manage-a-file-acl"></a>Управление ACL файла

Получите список управления доступом (ACL) файла, вызвав метод [даталакефилеклиент. жетакцессконтроласинк](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) и ЗАДАВ список ACL, вызвав метод [даталакефилеклиент. сетакцессконтроллист](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) .

> [!NOTE]
> Если приложение разрешает доступ с помощью Azure Active Directory (Azure AD), убедитесь, что участнику безопасности, используемому приложением для авторизации доступа, назначена [роль владельца данных BLOB-объекта хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и их влиянии на их изменение см. в разделе [Контроль доступа в Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

В этом примере получается и задается список управления доступом для файла с именем `my-file.txt`. Строка `user::rwx,group::r-x,other::rw-` предоставляет владельцам разрешения на чтение, запись и выполнение, предоставляет группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным разрешение на чтение и запись.

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

Сначала создайте экземпляр [даталакефилеклиент](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) , представляющий файл, который требуется скачать. Используйте метод [даталакефилеклиент. ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) и проанализируйте возвращаемое значение, чтобы получить объект [потока](https://docs.microsoft.com/dotnet/api/system.io.stream) . Используйте любой API-интерфейс обработки файлов .NET для сохранения байтов из потока в файл. 

В этом примере для сохранения байтов в файл используются объект [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) и [файловый поток](https://docs.microsoft.com/dotnet/api/system.io.filestream) . 

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

Перечислите содержимое каталога, вызвав метод [филесистемклиент. жетпассасинк](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) , а затем перечислить результаты.

В этом примере выводятся имена каждого файла, расположенного в каталоге с именем `my-directory`.

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

## <a name="see-also"></a>См. также раздел

* [Справочная документация по API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Пакет (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Примеры](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Сопоставление Gen1 с Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Отправить отзыв](https://github.com/Azure/azure-sdk-for-net/issues)

