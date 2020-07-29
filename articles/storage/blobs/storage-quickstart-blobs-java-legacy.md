---
title: Краткое руководство. Клиентская библиотека хранилища BLOB-объектов Azure V8 для Java
description: Создайте учетную запись хранения и контейнер в хранилище объектов (больших двоичных объектов). Затем с помощью клиентской библиотеки службы хранилища Azure V8 для Java отправьте большой двоичный объект в службу хранилища Azure, скачайте большой двоичный объект и перечислите большие двоичные объекты в контейнере.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ab45495283f8751912d93e9799958a8effc887a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323746"
---
# <a name="quickstart-manage-blobs-with-java-v8-sdk"></a>Краткое руководство. Управление большими двоичными объектами с помощью пакета SDK V8 для Java

Из этого краткого руководства вы узнаете, как управлять большими двоичными объектами с использованием Java. Большие двоичные объекты — это объекты, которые могут содержать большие объемы текстовых или двоичных данных, включая изображения, документы, потоковое мультимедиа и архивные данные. Вы будете отправлять, скачивать и перечислять большие двоичные объекты. Вы также создадите, настроите и удалите контейнеры.

> [!NOTE]
> В этом кратком руководстве используется устаревшая версия клиентской библиотеки Хранилища BLOB-объектов Azure. Чтобы начать работу с последней версией, см. раздел [Краткое руководство. Управление большими двоичными объектами с помощью пакета SDK для Java версии 12](storage-quickstart-blobs-java.md).

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.
- Учетная запись хранения Azure. [Создание учетной записи хранения](../common/storage-account-create.md).
- Интегрированная среда разработки с Maven интеграцией. В этом руководстве используется среда разработки [Eclipse](https://www.eclipse.org/downloads/) с конфигурацией для разработчиков Java.

## <a name="download-the-sample-application"></a>Загрузка примера приложения

[Пример приложения](https://github.com/Azure-Samples/storage-blobs-java-quickstart) — это базовое консольное приложение.

Используйте [Git](https://git-scm.com/) для загрузки копии приложения в среду разработки.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Эта команда клонирует репозиторий в локальную папку git. Чтобы открыть проект, запустите Eclipse и закройте экран приветствия. Выберите **файл** , а затем **откройте проекты из файловой системы**. Убедитесь, что флажок **Detect and configure project natures** (Обнаружить и настроить свойства проекта) установлен. Выберите **Directory** (Каталог) и перейдите к папке хранения клонированного репозитория. В клонированном репозитории выберите папку **blobAzureApp**. Убедитесь, что проект **blobAzureApp** отображается как проект Eclipse, затем выберите **Finish** (Готово).

После завершения импорта проекта откройте **AzureApp. Java** (находится в **блобкуиккстарт. блобазуреапп** внутри **src/Main/Java**) и замените `accountname` и `accountkey` внутри `storageConnectionString` строки. Затем запустите приложение. Конкретные инструкции по выполнению этих задач описаны в следующих разделах.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища

В приложении необходимо указать строку подключения для учетной записи хранения. Откройте файл **AzureApp.Java**. Найдите переменную `storageConnectionString` и вставьте значение строки подключения, скопированное в предыдущем разделе. Переменная `storageConnectionString` должна содержать данные, подобные приведенному ниже примеру кода:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Запуск примера

Этот пример приложения создает тестовый файл в каталоге по умолчанию (*C:\Users \<user> \AppData\Local\Temp*, для пользователей Windows), отправляет его в хранилище BLOB-объектов, выводит список больших двоичных объектов в контейнере, а затем скачивает файл с новым именем, чтобы можно было сравнить старые и новые файлы.

Запустите пример с помощью Maven в командной строке. Откройте оболочку и перейдите в папку **blobAzureApp** в клонированном каталоге. Затем введите `mvn compile exec:java`.

В следующем примере показаны выходные данные для запуска приложения в Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

Прежде чем продолжить, проверьте каталог по умолчанию (*C:\Users \<user> \AppData\Local\Temp*, для пользователей Windows) в качестве примера файла. Скопируйте URL-адрес BLOB-объекта из окна консоли и вставьте его в адресную строку браузера, чтобы просмотреть содержимое файла в хранилище BLOB-объектов. Если сравнить пример файла в каталоге с содержимым, хранящимся в хранилище BLOB-объектов, вы увидите, что они одинаковы.

  >[!NOTE]
  >Для просмотра файлов в хранилище BLOB-объектов можно также воспользоваться таким средством, как [обозреватель службы хранилища Azure](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Обозреватель службы хранилища Azure — это бесплатное кроссплатформенное средство для доступа к данным учетной записи хранения.

Убедившись, что файлы проверены, нажмите клавишу **Ввод** , чтобы завершить демонстрацию и удалить тестовые файлы. Теперь вы знаете, что делает этот пример. Давайте откроем файл **AzureApp.java** и изучим его код.

## <a name="understand-the-sample-code"></a>Разбор примера кода

Разберем пример кода, чтобы понять, как он работает.

### <a name="get-references-to-the-storage-objects"></a>Получение ссылок на объекты хранилища

Сначала необходимо создать ссылки на объекты, используемые для доступа к хранилищу BLOB-объектов и управлению им. Эти объекты зависят друг от друга — каждый объект используется следующим в списке объектом.

* Создайте экземпляр объекта [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) , указывающий на учетную запись хранения.

    Объект **CloudStorageAccount** представляет вашу учетную запись хранения и позволяет настраивать и использовать ее свойства программным способом. С помощью **CloudStorageAccount** можно создать экземпляр объекта **CloudBlobClient**, который необходим для доступа к службе BLOB-объектов.

* Создайте экземпляр объекта **CloudBlobClient** , указывающий на [службу BLOB-объектов](/java/api/com.microsoft.azure.storage.blob.cloudblobclient) в вашей учетной записи хранения.

    **CloudBlobClient** предоставляет точку доступа к службе BLOB-объектов, позволяя программно задавать свойства хранилища больших двоичных объектов и получать к ним доступ. С помощью **CloudBlobClient** можно создать экземпляр объекта **CloudBlobContainer**, который необходим для создания контейнеров.

* Создайте экземпляр объекта [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer), представляющий контейнер, к которому осуществляется доступ. Используйте контейнеры для организации больших двоичных объектов аналогично папкам для упорядочения файлов на компьютере.

    После создания **CloudBlobContainer**можно создать экземпляр объекта [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob) , указывающий на конкретный большой двоичный объект, который вас интересует, и выполнить отправку, скачивание, копирование или другую операцию.

> [!IMPORTANT]
> Имена контейнеров должны состоять из знаков нижнего регистра. Дополнительные сведения о контейнерах см. в статье [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

### <a name="create-a-container"></a>Создание контейнера

В этом разделе описано создание экземпляров объектов, создание контейнера и последующее задание разрешений для контейнера, что позволяет предоставить общий доступ к большим двоичным объектам по URL-адресу. Контейнер называется **quickstartcontainer**.

В этом примере используется [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists) , так как нам нужно создать новый контейнер при каждом запуске примера. В рабочей среде, где вы используете один и тот же контейнер во всех приложениях, рекомендуется вызывать **CreateIfNotExists** только один раз. Вы можете также создать контейнер заранее, чтобы не создавать его в коде.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Отправка BLOB-объектов в контейнер

Чтобы отправить файл в блочный BLOB-объект, получите ссылку на этот объект в целевом контейнере. При наличии ссылки на BLOB-объект вы можете передать в него данные с помощью [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload). Эта операция создает большой двоичный объект, если он еще не существует, или заменяет его, если существует.

В примере кода создается локальный файл, который будет использоваться для передачи и скачивания. Передаваемый файл сохраняется как **source**, и большому двоичному объекту присваивается имя в **blob**. В приведенном ниже примере файл отправляется в контейнер с именем **quickstart**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Есть несколько методов `upload`, которые можно использовать с хранилищем BLOB-объектов, включая [upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadstandardblobtier) и [uploadText](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadtext). Для строки, например, лучше использовать метод `UploadText`, а не `Upload`.

Блочный BLOB-объект может представлять собой текстовый или двоичный файл любого типа. Страничные BLOB-объекты в основном используются для файлов VHD, применяемых для поддержки виртуальных машин IaaS. Используйте добавочные большие двоичные объекты для ведения журнала, например, если требуется выполнять запись в файл и добавлять дополнительные сведения. Большинство объектов, находящихся в хранилище BLOB-объектов, представляют собой блочные BLOB-объекты.

### <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Получить список файлов в контейнере можно с помощью метода [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobs). Следующий код извлекает список BLOB-объектов, затем переходит по ним, отображая найденные URI. Можно скопировать URI из окна командной строки и вставить его в адресную строку браузера для просмотра файла.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Скачивание больших двоичных объектов

Скачайте большие двоичные объекты на локальный диск с помощью метода [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.downloadtofile).

Следующий код скачивает BLOB-объект, отправленный в предыдущем разделе, добавляя к имени BLOB-объекта суффикс "_DOWNLOADED", чтобы вы увидели оба файла на локальном диске.

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and
// haven't changed the blob we're interested in, so we can reuse it.
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны отправленные большие двоичные объекты, удалите весь контейнер с помощью метода [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists). Он также удаляет файлы в контейнере.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();

if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве описано, как передавать файлы между локальным диском и хранилищем BLOB-объектов Azure с помощью Java. Чтобы узнать подробнее о работе с Java, перейдите в репозиторий исходного кода на GitHub.

> [!div class="nextstepaction"]
> Справочник по API [Java](https://docs.microsoft.com/java/api/overview/azure/storage?view=azure-java-legacy) 
>  [Примеры кода для Java](../common/storage-samples-java.md)
