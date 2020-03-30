---
title: Лазурное хранилище озер Gen2 Java SDK для файлов & ACLs
description: Используйте библиотеки хранения Azure для Java для управления каталогами и списками управления доступом файлов и каталогов (ACL) в учетных записях хранения с включенным иерархическим пространством имен (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 45870dd7d3035b6b49340fd6e8016794088e775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061560"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Используйте Java для управления каталогами, файлами и ACL в Azure Data Lake Storage2

В этой статье показано, как использовать Java для создания и управления каталогами, файлами и разрешениями в учетных записях хранения с включенным иерархическим пространством имен (HNS). 

[Пакет (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Образцы](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API ссылка](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [Gen1 на Gen2 отображение](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | Дать[обратную связь](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранилища с включенным иерархическим пространством имен (HNS). Следуйте [этим](data-lake-storage-quickstart-create-account.md) инструкциям, чтобы создать один.

## <a name="set-up-your-project"></a>Настройка проекта

Чтобы начать работу, откройте [эту страницу](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) и найдите последнюю версию библиотеки Java. Затем откройте файл *pom.xml* в текстовом редакторе. Добавьте элемент зависимости, который ссылается на эту версию.

Если вы планируете аутентифицировать клиентское приложение с помощью Azure Active Directory (AD), добавьте зависимость в Секретную клиентскую библиотеку Azure. Смотрите [Добавление пакета Секретной библиотеки клиентов в ваш проект.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)

Затем добавьте эти заявления об импорте в файл кода.

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Подключение к учетной записи 

Чтобы использовать фрагменты в этой статье, необходимо создать экземпляр **DataLakeServiceClient,** представляющий учетную запись хранилища. 

### <a name="connect-by-using-an-account-key"></a>Подключение с помощью ключа учетной записи

Это самый простой способ подключения к учетной записи. 

Этот пример создает экземпляр **DataLakeServiceClient** с помощью ключа учетной записи.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Подключение с помощью активного каталога Azure (Azure AD)

Для проверки подлинности приложения с помощью Azure AD можно использовать [библиотеку клиентов итога «Azure» для Java.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)

Этот пример создает экземпляр **DataLakeServiceClient,** используя идентификатор клиента, секрет клиента и идентификатор клиента.  Чтобы получить эти значения, [см. Приобрести токен из Azure AD для авторизации запросов из клиентского приложения.](../common/storage-auth-aad-app.md)

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Для получения дополнительных примеров можно найти [в библиотеке клиентов иотекОв Azure для](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) документации Java.


## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система действует как контейнер для файлов. Вы можете создать его, позвонив по методу **DataLakeServiceClient.createFileSystem.**

Этот пример создает файловую систему под названием `my-file-system`. 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на каталог, позвонив в метод **DataLakeFileSystemClient.createDirectory.**

Этот пример добавляет каталог `my-directory` с именем в файловую систему, `my-subdirectory`а затем добавляет подкаталог с именем . 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуй или переместите каталог, позвонив в метод **DataLakeDirectoryClient.rename.** Передайте путь желаемого каталога параметру. 

Этот пример переименовывает подкаталог `my-subdirectory-renamed`к имени .

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

Этот пример перемещает каталог `my-subdirectory-renamed` с именем в подкаталог `my-directory-2`каталог аменем . 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалить каталог, позвонив в **метод DataLakeDirectoryClient.deleteWithResponse.**

Этот пример удаляет каталог `my-directory`с именем .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Управление каталогом ACL

Этот пример получает, а затем устанавливает ACL каталога с именем `my-directory`. Этот пример дает пользователю-владельцев читать, писать и выполнять разрешения, дает группе владельцев только читать и выполнять разрешения, и дает всем другим доступ к чтению.

> [!NOTE]
> Если приложение разрешает доступ с помощью Active Directory Azure (Azure AD), убедитесь, что принципбезопасности, который приложение использует для авторизации доступа, был назначен [роль владельца данных Storage Blob.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Чтобы узнать больше о том, как применяются разрешения [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ACL и о последствиях их изменения, см.

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>Загрузить файл в каталог

Во-первых, создайте ссылку на файл в целевом каталоге, создав экземпляр класса **DataLakeFileClient.** Загрузите файл, позвонив в метод **DataLakeFileClient.append.** Убедитесь в том, чтобы завершить загрузку, позвонив **в DataLakeFileClient.FlushAsync** метод.

Этот пример загружает текстовый `my-directory`файл в каталог с именем .

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> Если размер файла большой, ваш код должен будет сделать несколько звонков в метод **DataLakeFileClient.append.** Вместо этого рассмотрите возможность использования метода **DataLakeFileClient.uploadFromFile.** Таким образом, вы можете загрузить весь файл в один вызов. 
>
> Пример см. в следующем разделе.

## <a name="upload-a-large-file-to-a-directory"></a>Загрузить большой файл в каталог

Используйте метод **DataLakeFileClient.uploadFromFile** для загрузки больших файлов без необходимости совершать несколько звонков на метод **DataLakeFileClient.append.**

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>Управление файлом ACL

Этот пример получает, а затем устанавливает `upload-file.txt`ACL файла с именем. Этот пример дает пользователю-владельцев читать, писать и выполнять разрешения, дает группе владельцев только читать и выполнять разрешения, и дает всем другим доступ к чтению.

> [!NOTE]
> Если приложение разрешает доступ с помощью Active Directory Azure (Azure AD), убедитесь, что принципбезопасности, который приложение использует для авторизации доступа, был назначен [роль владельца данных Storage Blob.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Чтобы узнать больше о том, как применяются разрешения [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ACL и о последствиях их изменения, см.

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>Скачать из каталога

Во-первых, создайте экземпляр **DataLakeFileClient,** представляющий файл, который вы хотите загрузить. Используйте метод **DataLakeFileClient.read** для чтения файла. Используйте aPI обработки файлов .NET, чтобы сохранить байты из потока в файл. 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Этот пример печатает имена каждого файла, который находится `my-directory`в каталоге с именем .

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>См. также

* [Справочная документация по API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [Пакет (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Примеры](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Отображение Gen1 до Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Дайте обратную связь](https://github.com/Azure/azure-sdk-for-java/issues)