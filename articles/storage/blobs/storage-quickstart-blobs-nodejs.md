---
title: Краткое руководство. Использование библиотеки хранилища BLOB-объектов Azure версии 12 для JavaScript
description: В этом кратком руководстве вы узнаете, как использовать клиентскую библиотеку службы хранилища BLOB-объектов Azure версии 12 для JavaScript для создания контейнера и большого двоичного объекта в хранилище BLOB-объектов. Далее вы узнаете, как скачать большой двоичный объект на локальный компьютер и как получить список всех больших двоичных объектов в контейнере.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 28a75158b161e680f857b986bcb754f1f99e8fab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825303"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>Краткое руководство. Использование клиентской библиотеки хранилища BLOB-объектов Azure версии 12 для JavaScript

Приступите к работе с клиентской библиотекой хранилища BLOB-объектов Azure версии 12 для JavaScript. Хранилище BLOB-объектов Azure — это решение корпорации Майкрософт для хранения объектов в облаке. Чтобы установить пакет и испробовать пример кода для выполнения базовых задач, выполните приведенные здесь действия. Хранилище BLOB-объектов оптимизировано для хранения больших объемов неструктурированных данных.

> [!NOTE]
> Чтобы приступить к работе с предыдущей версией пакета SDK, обратитесь к разделу [Краткое руководство. Клиентская библиотека хранилища BLOB-объектов Azure для JavaScript](storage-quickstart-blobs-nodejs-v10.md).

Клиентскую библиотеку хранилища BLOB-объектов Azure версии 12 для JavaScript можно использовать для выполнения следующих задач:

* Создание контейнера
* передача большого двоичного объекта в хранилище Azure;
* перечисление всех больших двоичных объектов в контейнере;
* скачивание большого двоичного объекта на локальный компьютер;
* Удаление контейнера

[Справочная документация по API](/javascript/api/@azure/storage-blob) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [Пакет (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [Примеры](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Учетная запись хранения Azure — [создайте такую учетную запись](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
* Последняя версия [Node.js](https://nodejs.org/en/download/) для вашей операционной системы.

## <a name="setting-up"></a>Настройка

В этом разделе рассматривается подготовка проекта для работы с клиентской библиотекой хранилища BLOB-объектов Azure версии 12 для JavaScript.

### <a name="create-the-project"></a>Создание проекта

Создайте приложение JavaScript с именем *blob-quickstart-v12*.

1. В окне консоли (командная строка, PowerShell или Bash) создайте каталог для проекта.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Перейдите в только что созданный каталог *blob-quickstart-v12*.

    ```console
    cd blob-quickstart-v12
    ```

1. Создайте текстовый файл *package.json*. Этот файл определяет проект Node.js. Сохраните этот файл в каталоге *blob-quickstart-v12*. Ниже приведено содержимое файла.

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    Вы можете указать собственное имя в поле `author`, если хотите.
   
### <a name="install-the-package"></a>Установка пакета

Оставаясь в каталоге *blob-quickstart-v12*, установите клиентскую библиотеку хранилища BLOB-объектов Azure для пакета JavaScript с помощью команды `npm install`. Эта команда считывает файл *package.json* и устанавливает клиентскую библиотеку хранилища BLOB-объектов Azure версии 12 для пакета JavaScript и все библиотеки, от которых она зависит.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Настройка платформы приложения

Из каталога проекта:

1. Откройте другой новый текстовый файл в редакторе кода.
1. Добавьте в него вызовы `require` для загрузки модулей Azure и Node.js.
1. Создайте структуру программы, включая самую простую обработку исключений.

    Вот этот код:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - Javascript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Сохраните новый файл как *blob-quickstart-v12.js* в каталоге *blob-quickstart-v12*.

### <a name="copy-your-credentials-from-the-azure-portal"></a>Копирование учетных данных с портала Azure

Когда пример приложения выполняет запрос к службе хранилища Azure, он должен быть авторизован. Для авторизации запроса добавьте в приложение учетные данные учетной записи хранения в виде строки подключения. Чтобы просмотреть учетные данные учетной записи хранения, выполните следующие действия:

1. Войдите на [портале Azure](https://portal.azure.com).
2. Перейдите к учетной записи хранения.
3. В разделе **Параметры** учетной записи хранения выберите параметр **Ключи доступа**. На этой странице вы увидите ключи доступа к учетной записи и полную строку подключения для каждого ключа.
4. Найдите значение для параметра **Строка подключения** в разделе **Key1** и нажмите кнопку **Скопировать**, чтобы скопировать строку подключения. На следующем этапе вы добавите значение строки подключения в переменную среды.

    ![Снимок экрана, на котором показано, как скопировать строку подключения с портала Azure](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища

После копирования строки подключения запишите ее в переменной среды на локальном компьютере, где выполняется приложение. Чтобы задать переменную среды, откройте окно консоли и следуйте инструкциям для используемой операционной системы. Замените `<yourconnectionstring>` фактической строкой подключения.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

После добавления переменной среды в Windows вам необходимо запустить новый экземпляр командного окна.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Перезапуск программ

После добавления переменной среды перезапустите все запущенные программы, которым может понадобиться считать переменную среды. Например, перезапустите среду разработки или редактор, прежде чем продолжить.

## <a name="object-model"></a>Объектная модель

Хранилище BLOB-объектов Azure оптимизировано для хранения больших объемов неструктурированных данных. Неструктурированные данные — это данные, которые не соответствуют определенной модели данных или определению, например текстовых или двоичных данных. В хранилище BLOB-объектов предлагается три типа ресурсов:

* учетная запись хранения;
* контейнер в учетной записи хранения;
* большой двоичный объект в контейнере.

На следующей схеме показана связь между этими ресурсами.

![Схема архитектуры службы хранилища BLOB-объектов](./media/storage-blob-introduction/blob1.png)

Используйте следующие классы JavaScript для взаимодействия с этими ресурсами.

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient). Класс `BlobServiceClient` позволяет управлять ресурсами службы хранилища Azure и контейнерами больших двоичных объектов.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient). Класс `ContainerClient` позволяет управлять контейнерами службы хранилища Azure и содержащимися в них большими двоичными объектами.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient). Класс `BlobClient` позволяет управлять большими двоичными объектами службы хранилища Azure.

## <a name="code-examples"></a>Примеры кода

В этих примерах фрагментов кода показано, как выполнять следующие действия с помощью клиентской библиотеки хранилища BLOB-объектов Azure для JavaScript:

* [Получение строки подключения](#get-the-connection-string)
* [Создание контейнера](#create-a-container)
* [отправка больших двоичных объектов в контейнер](#upload-blobs-to-a-container);
* [перечисление больших двоичных объектов в контейнере](#list-the-blobs-in-a-container);
* [скачивание больших двоичных объектов](#download-blobs);
* [Удаление контейнера](#delete-a-container)

### <a name="get-the-connection-string"></a>Получение строки подключения

Приведенный ниже код извлекает строку подключения для учетной записи хранения из переменной среды, созданной в разделе [Настройка строки подключения хранилища](#configure-your-storage-connection-string).

Добавьте этот код в функцию `main`.

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>Создание контейнера

Выберите имя нового контейнера. Приведенный ниже код добавляет к имени контейнера значение UUID, чтобы сделать это имя уникальным.

> [!IMPORTANT]
> Имена контейнеров должны состоять из знаков нижнего регистра. Дополнительные сведения об именовании контейнеров и больших двоичных объектов см. в статье [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

Создайте экземпляр класса [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient), вызвав метод [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--newpipelineoptions-). Затем вызовите метод [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-), чтобы получить ссылку на контейнер. Наконец, вызовите метод [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-), чтобы создать контейнер в учетной записи хранения.

Добавьте следующий код в конец функции `main`.

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await new BlobServiceClient.fromConnectionString(CONNECT_STR);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
await containerClient.create();
```

### <a name="upload-blobs-to-a-container"></a>Отправка больших двоичных объектов в контейнер

Приведенный ниже фрагмент кода:

1. Создает текстовую строку для передачи в большой двоичный объект.
1. Получает ссылку на объект [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient), вызывая метод [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) для [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) из раздела [Создание контейнера](#create-a-container).
1. Передает данные текстовой строки в большой двоичный объект, вызывая метод [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-).

Добавьте следующий код в конец функции `main`.

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure Storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
await blockBlobClient.upload(data, data.length);
```

### <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Выведите список больших двоичных объектов в контейнере, вызвав метод [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-). В этом случае в контейнер был добавлен лишь один большой двоичный объект, поэтому операция перечисления возвращает только его.

Добавьте следующий код в конец функции `main`.

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Скачивание больших двоичных объектов

Скачайте созданный ранее большой двоичный объект, вызвав метод [download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-). В примере кода имеется вспомогательная функция `streamToString`, которая используется для чтения потока Node.js в строку.

Добавьте следующий код в конец функции `main`.

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Добавьте эту вспомогательную функцию *после* функции `main`.

```javascript
// A helper function used to read a Node.js readable stream into a string
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
```

### <a name="delete-a-container"></a>Удаление контейнера

Следующий код очищает созданные приложением ресурсы, полностью удаляя контейнер с помощью метода [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-). Кроме того, при необходимости можно удалить локальные файлы.

Добавьте следующий код в конец функции `main`.

```javascript
console.log('\nDeleting container...');

// Delete container
await containerClient.delete();
```

## <a name="run-the-code"></a>Выполнение кода

Это приложение создает текстовую строку и передает ее в хранилище BLOB-объектов. Затем пример выводит список больших двоичных объектов в контейнере, скачивает большой двоичный объект и отображает скачанные данные.

В окне консоли перейдите в каталог, содержащий файл *blob-quickstart-v12.py*, а затем выполните указанную команду `node`, чтобы запустить приложение.

```console
node blob-quickstart-v12.js
```

Вы должны увидеть выходные данные приложения, как показано ниже.

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Пошагово выполните код в отладчике и просматривайте результаты на портале Azure. Проверьте, создан ли контейнер. Вы можете открыть большой двоичный объект в контейнере и просмотреть его содержимое.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как передавать и скачивать большие двоичные объекты, а также выводить их список с помощью JavaScript.

Чтобы просмотреть примеры приложений для хранилища BLOB-объектов, перейдите к следующему разделу:

> [!div class="nextstepaction"]
> [Примеры для пакета SDK хранилища BLOB-объектов Azure версии 12 для JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/azure-storage-blob/samples)

* Чтобы узнать больше, ознакомьтесь с [пакетом SDK Azure для JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md).
* Руководства, примеры, краткие руководства и другую документацию можно найти на странице [Документация по пакету SDK для JavaScript](/azure/javascript/).
