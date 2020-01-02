---
title: Использование Azure .NET для файлов & ACL в Azure Data Lake Storage 2-го поколения (Предварительная версия)
description: Используйте клиентскую библиотеку службы хранилища Azure для управления каталогами и списками управления доступом к файлам и каталогам (ACL) в учетных записях хранения с включенным иерархическое пространством имен (HNS).
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a756518688b5b1f8b854165de69d3444b772eabc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931223"
---
# <a name="use-net-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Использование .NET для файлов & ACL в Azure Data Lake Storage 2-го поколения (Предварительная версия)

В этой статье показано, как использовать .NET для создания каталогов, файлов и разрешений в учетных записях хранения с включенным иерархическое пространством имен (HNS) и управления ими. 

> [!IMPORTANT]
> Пакет [Azure. Storage. Files. Data Lake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) NuGet в этой статье сейчас находится в общедоступной предварительной версии.

[Пакеты (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) | [примеры](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [справочника по API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html) | [Gen1 Gen2 сопоставления](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Отправить отзыв](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Технические условия

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранения, в которой включено иерархическое пространство имен (HNS). Чтобы создать его, выполните [следующие](data-lake-storage-quickstart-create-account.md) инструкции.

## <a name="set-up-your-project"></a>Настройка проекта

Чтобы приступить к работе, установите пакет NuGet [Azure. Storage. Files. Data Lake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

Дополнительные сведения об установке пакетов NuGet см. в разделе [Установка пакетов и управление ими в Visual Studio с помощью диспетчера пакетов NuGet](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Затем добавьте эти операторы using в начало файла кода.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Подключение к учетной записи

Чтобы использовать фрагменты кода в этой статье, необходимо создать экземпляр **даталакесервицеклиент** , представляющий учетную запись хранения. Самый простой способ получить его — использовать ключ учетной записи. 

В этом примере создается экземпляр **даталакесервицеклиент** с помощью ключа учетной записи.

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

Файловая система выступает в качестве контейнера для файлов. Его можно создать, вызвав метод **филесистемклиент. креатефилесистемасинк** .

В этом примере создается файловая система с именем `my-file-system`. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>создать каталог;

Создайте ссылку на каталог, вызвав метод **филесистемклиент. креатедиректорясинк** .

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

Переименуйте или переместите каталог, вызвав метод **директориклиент. ренамеасинк** . Передайте путь к нужному каталогу параметру. 

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

Удалите каталог, вызвав метод **директориклиент. Delete** .

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

Получите список управления доступом (ACL) каталога, вызвав метод **директориклиент. жетакцессконтроласинк** и ЗАДАВ список ACL, вызвав метод **директориклиент. сетакцессконтрол** .

В этом примере получается и задается список ACL для каталога с именем `my-directory`. Строка `user::rwx,group::r-x,other::rw-` предоставляет владельцам разрешения на чтение, запись и выполнение, предоставляет группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным разрешение на чтение и запись.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.Acl);

    directoryClient.SetAccessControl("user::rwx,group::r-x,other::rw-");

}

```

## <a name="upload-a-file-to-a-directory"></a>Передача файла в каталог

Сначала создайте ссылку на файл в целевом каталоге, создав экземпляр класса **даталакефилеклиент** . Отправьте файл, вызвав метод **даталакефилеклиент. аппендасинк** . Обязательно завершите передачу, вызвав метод **даталакефилеклиент. FlushAsync** .

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

Получите список управления доступом (ACL) файла, вызвав метод **даталакефилеклиент. жетакцессконтроласинк** и ЗАДАВ список ACL, вызвав метод **филеклиент. сетакцессконтрол** .

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

    Console.WriteLine(FileAccessControl.Acl);

    fileClient.SetAccessControl("user::rwx,group::r-x,other::rw-");
}
```

## <a name="download-from-a-directory"></a>Скачать из каталога 

Сначала создайте экземпляр **даталакефилеклиент** , представляющий файл, который требуется скачать. Используйте метод **филеклиент. ReadAsync** и проанализируйте возвращаемое значение, чтобы получить объект [потока](https://docs.microsoft.com/dotnet/api/system.io.stream) . Используйте любой API-интерфейс обработки файлов .NET для сохранения байтов из потока в файл. 

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

Перечислите содержимое каталога, вызвав метод **филесистемклиент. листпассасинк** , а затем перечислить результаты.

В этом примере выводятся имена каждого файла, расположенного в каталоге с именем `my-directory`.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.ListPathsAsync("my-directory").GetAsyncEnumerator();

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

## <a name="see-also"></a>Дополнительные материалы

* [Справочная документация по API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html)
* [Пакет (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6)
* [Примеры](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Сопоставление Gen1 с Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Отправить отзыв](https://github.com/Azure/azure-sdk-for-net/issues)

