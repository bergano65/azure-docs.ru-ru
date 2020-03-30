---
title: Используйте JavaScript для файлов & ВАК в Azure Data Lake Storage2
description: Используйте библиотеку клиентов Azure Storage Data Lake для JavaScript для управления каталогами и списками управления доступом файлов и каталогов (ACL) в учетных записях хранения с включенным иерархическим пространством имен (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061538"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Используйте JavaScript для управления каталогами, файлами и ACL в Azure Data Lake Storage2

В этой статье показано, как использовать JavaScript для создания и управления каталогами, файлами и разрешениями в учетных записях хранения с включенным иерархическим пространством имен (HNS). 

[Пакет (менеджер узла пакета)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [Образцы](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [Дают обратную связь](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранилища с включенным иерархическим пространством имен (HNS). Следуйте [этим](data-lake-storage-quickstart-create-account.md) инструкциям, чтобы создать один.
> * Если вы используете этот пакет в приложении Node.js, вам понадобится Node.js 8.0.0 или выше.

## <a name="set-up-your-project"></a>Настройка проекта

Установите клиентскую библиотеку Data Lake для JavaScript, открыв окно терминала, а затем введя следующую команду.

```javascript
npm install @azure/storage-file-datalake
```

Импортируйте `storage-file-datalake` пакет, разместив это заявление в верхней части файла кода. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Подключение к учетной записи 

Чтобы использовать фрагменты в этой статье, необходимо создать экземпляр **DataLakeServiceClient,** представляющий учетную запись хранилища. 

### <a name="connect-by-using-an-account-key"></a>Подключение с помощью ключа учетной записи

Это самый простой способ подключения к учетной записи. 

Этот пример создает экземпляр **DataLakeServiceClient** с помощью ключа учетной записи.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Этот метод авторизации работает только для приложений Node.js. Если вы планируете запустить свой код в браузере, вы можете авторизовать с помощью Active Directory (AD) Azure Active. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Подключение с помощью активного каталога Azure (AD)

Для проверки подлинности приложения с помощью Azure AD можно использовать [библиотеку клиентов итогов Azure.](https://www.npmjs.com/package/@azure/identity)

Этот пример создает экземпляр **DataLakeServiceClient,** используя идентификатор клиента, секрет клиента и идентификатор клиента.  Чтобы получить эти значения, [см. Приобрести токен из Azure AD для авторизации запросов из клиентского приложения.](../common/storage-auth-aad-app.md)

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Для получения дополнительных примеров можно найти [библиотеку клиентов иотек Иждиaz для](https://www.npmjs.com/package/@azure/identity) документации JS.

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система действует как контейнер для файлов. Вы можете создать один, получив экземпляр **FileSystemClient,** а затем позвонив в **Метод FileSystemClient.Create.**

Этот пример создает файловую систему под названием `my-file-system`. 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку каталога, получив экземпляр **directoryClient,** а затем позвонив в метод **DirectoryClient.create.**

Этот пример добавляет каталог, названный `my-directory` в файловую систему. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуй или переместите каталог, позвонив в метод **каталогаClient.rename.** Передайте путь желаемого каталога параметру. 

Этот пример переименовывает подкаталог `my-directory-renamed`к имени .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Этот пример перемещает каталог `my-directory-renamed` с именем в подкаталог `my-directory-2`каталог аменем . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалить каталог, позвонив в метод **DirectoryClient.delete.**

Этот пример удаляет каталог `my-directory`с именем .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Управление каталогом ACL

Этот пример получает, а затем устанавливает ACL каталога с именем `my-directory`. Этот пример дает пользователю-владельцев читать, писать и выполнять разрешения, дает группе владельцев только читать и выполнять разрешения, и дает всем другим доступ к чтению.

> [!NOTE]
> Если приложение разрешает доступ с помощью Active Directory Azure (Azure AD), убедитесь, что принципбезопасности, который приложение использует для авторизации доступа, был назначен [роль владельца данных Storage Blob.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Чтобы узнать больше о том, как применяются разрешения [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ACL и о последствиях их изменения, см.

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>Загрузить файл в каталог

Во-первых, прочитайте файл. В этом примере используется `fs` модуль Node.js. Затем создайте ссылку на файл в целевом каталоге, создав экземпляр **FileClient,** а затем вызывая метод **FileClient.create.** Загрузите файл, позвонив в метод **FileClient.append.** Убедитесь в том, чтобы завершить загрузку, позвонив в **метод FileClient.flush.**

Этот пример загружает текстовый `my-directory`файл в каталог с именем .

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>Управление файлом ACL

Этот пример получает, а затем устанавливает `upload-file.txt`ACL файла с именем. Этот пример дает пользователю-владельцев читать, писать и выполнять разрешения, дает группе владельцев только читать и выполнять разрешения, и дает всем другим доступ к чтению.

> [!NOTE]
> Если приложение разрешает доступ с помощью Active Directory Azure (Azure AD), убедитесь, что принципбезопасности, который приложение использует для авторизации доступа, был назначен [роль владельца данных Storage Blob.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Чтобы узнать больше о том, как применяются разрешения [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ACL и о последствиях их изменения, см.

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>Скачать из каталога

Во-первых, создайте экземпляр **FileSystemClient,** представляющий файл, который вы хотите загрузить. Используйте метод **FileSystemClient.read** для чтения файла. Затем напишите файл. Для этого в этом примере используется модуль Node.js. `fs` 

> [!NOTE]
> Этот метод загрузки файла работает только для приложений Node.js. Если вы планируете запустить свой код в браузере, смотрите [в библиотеке клиентов Azure Storage File Data Lake для](https://www.npmjs.com/package/@azure/storage-file-datalake) файла чтения JavaScript для примера того, как это сделать в браузере. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Этот пример печатает имена каждого каталога и файла, который `my-directory`находится в каталоге с именем .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>См. также

* [Пакет (менеджер узла пакета)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Примеры](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Дайте обратную связь](https://github.com/Azure/azure-sdk-for-java/issues)