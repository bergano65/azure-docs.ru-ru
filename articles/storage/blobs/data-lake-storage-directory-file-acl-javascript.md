---
title: Использование JavaScript для файлов & ACL в Azure Data Lake Storage 2-го поколения
description: Используйте клиентскую библиотеку службы хранилища Azure Data Lake для JavaScript, чтобы управлять каталогами и списками управления доступом к файлам и каталогам (ACL) в учетных записях хранения с включенным иерархическое пространством имен (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: c774d3f56eaf666a31ff73f433a3b4a5a363ce2f
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142498"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Использование JavaScript для управления каталогами, файлами и списками ACL в Azure Data Lake Storage 2-го поколения

В этой статье показано, как использовать JavaScript для создания каталогов, файлов и разрешений в учетных записях хранения с включенным иерархическое пространством имен (HNS) и управления ими. 

[Пакет (диспетчер пакетов node)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Примеры](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Отправить отзыв](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * В учетной записи хранения включено иерархическое пространство имен. Выполните [эти](data-lake-storage-quickstart-create-account.md) инструкции, чтобы создать учетную запись.
> * Если вы используете этот пакет в Node.js приложении, потребуется Node.js 8.0.0 или более поздней версии.

## <a name="set-up-your-project"></a>Настройка проекта

Установите Data Lake клиентскую библиотеку для JavaScript, открыв окно терминала, а затем введя следующую команду.

```javascript
npm install @azure/storage-file-datalake
```

Импортируйте `storage-file-datalake` пакет, поместив этот оператор в начало файла кода. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Подключение к учетной записи 

Чтобы использовать фрагменты кода в этой статье, необходимо создать экземпляр **даталакесервицеклиент** , представляющий учетную запись хранения. 

### <a name="connect-by-using-an-account-key"></a>Подключение с помощью ключа учетной записи

Это самый простой способ подключения к учетной записи. 

В этом примере создается экземпляр **даталакесервицеклиент** с помощью ключа учетной записи.

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
> Этот метод авторизации работает только для приложений Node.js. Если вы планируете выполнять код в браузере, можно авторизовать с помощью Azure Active Directory (AD). 

### <a name="connect-by-using-azure-active-directory-ad"></a>Подключение с помощью Azure Active Directory (AD)

Вы можете использовать [клиентскую библиотеку Azure Identity для JS](https://www.npmjs.com/package/@azure/identity) для проверки подлинности приложения в Azure AD.

В этом примере создается экземпляр **даталакесервицеклиент** с использованием идентификатора клиента, секрета клиента и идентификатора клиента.  Чтобы получить эти значения, см. статью [Получение маркера из Azure AD для авторизации запросов из клиентского приложения](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Дополнительные примеры см. в документации по [клиентской библиотеке удостоверений Azure для JS](https://www.npmjs.com/package/@azure/identity) .

## <a name="create-a-container"></a>Создание контейнера

Контейнер выступает в качестве файловой системы для файлов. Его можно создать, получив экземпляр **филесистемклиент** , а затем вызвав метод **филесистемклиент. Create** .

В этом примере создается контейнер с именем `my-file-system` . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на каталог, получив экземпляр **директориклиент** , а затем вызвав метод **директориклиент. Create** .

В этом примере в контейнер добавляется каталог с именем `my-directory` . 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуйте или переместите каталог, вызвав метод **директориклиент. Rename** . Передайте путь к нужному каталогу параметру. 

В этом примере подкаталог переименовывается в имя `my-directory-renamed` .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

В этом примере каталог с именем перемещается `my-directory-renamed` в подкаталог каталога с именем `my-directory-2` . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог, вызвав метод **директориклиент. Delete** .

В этом примере удаляется каталог `my-directory`.   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Управление ACL каталога

В этом примере получается и затем задается список ACL для каталога с именем `my-directory` . В этом примере предоставляются права владельца «чтение», «запись» и «выполнение», которая предоставляет группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным доступ для чтения.

> [!NOTE]
> Если приложение разрешает доступ с помощью Azure Active Directory (Azure AD), убедитесь, что участнику безопасности, используемому приложением для авторизации доступа, назначена [роль владельца данных BLOB-объекта хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и последствиях их изменения см. на странице [Контроль доступа в Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

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

## <a name="upload-a-file-to-a-directory"></a>Отправка файла в каталог

Сначала Считайте файл. В этом примере используется `fs` модуль Node.js. Затем создайте ссылку на файл в целевом каталоге, создав экземпляр **филеклиент** , а затем вызвав метод **филеклиент. Create** . Отправьте файл, вызвав метод **филеклиент. append** . Обязательно завершите передачу, вызвав метод **филеклиент. Flush** .

В этом примере текстовый файл перегружается в каталог с именем `my-directory` .

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

## <a name="manage-a-file-acl"></a>Управление ACL файла

Этот пример получает и затем задает список управления доступом для файла с именем `upload-file.txt` . В этом примере предоставляются права владельца «чтение», «запись» и «выполнение», которая предоставляет группе-владельцу только разрешения на чтение и выполнение, а также предоставляет всем остальным доступ для чтения.

> [!NOTE]
> Если приложение разрешает доступ с помощью Azure Active Directory (Azure AD), убедитесь, что участнику безопасности, используемому приложением для авторизации доступа, назначена [роль владельца данных BLOB-объекта хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и последствиях их изменения см. на странице [Контроль доступа в Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

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

## <a name="download-from-a-directory"></a>Скачивание из каталога

Сначала создайте экземпляр **филесистемклиент** , представляющий файл, который требуется скачать. Для чтения файла используйте метод **филесистемклиент. Read** . Затем запишите файл. В этом примере `fs` для этого используется модуль Node.js. 

> [!NOTE]
> Этот метод загрузки файла работает только для приложений Node.js. Если вы планируете выполнять код в браузере, см. пример того, как это сделать в браузере, с помощью файла сведений о [библиотеке Data Lake клиентской библиотеки для JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) . 

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

В этом примере выводятся имена каждого каталога и файла, расположенного в каталоге с именем `my-directory` .

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

* [Пакет (диспетчер пакетов node)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Примеры](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Отправить отзыв](https://github.com/Azure/azure-sdk-for-java/issues)