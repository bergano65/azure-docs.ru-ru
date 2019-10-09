---
title: Разработка для службы файлов Azure с помощью .NET | Документация Майкрософт
description: Узнайте, как разрабатывать приложения и службы .NET, использующие службу файлов Azure для хранения файлов данных.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6f2159ddf3e3039dc0c38fc8f942c508ac177f06
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038186"
---
# <a name="develop-for-azure-files-with-net"></a>Разработка для службы файлов Azure с помощью .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

В этом руководстве рассматриваются основы использования .NET для разработки приложений, использующих [службу файлов Azure](storage-files-introduction.md) для хранения данных файлов. В этом руководстве создается простое консольное приложение для основных действий с файлами .NET и Azure.

* Получение содержимого файла.
* Задайте максимальный размер или *квоту* для общей папки.
* Создайте подпись общего доступа (ключ SAS) для файла, который использует политику общего доступа, определенную в общей папке.
* Скопируйте файл в другой файл в той же учетной записи хранения
* Скопируйте файл в BLOB-объект в той же учетной записи хранения
* Используйте метрики службы хранилища Azure для устранения неполадок.

Дополнительные сведения о службе файлов Azure см. в статье [что такое служба файлов Azure?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Основные сведения об API-интерфейсах .NET

Служба файлов Azure предлагает два широких подхода к использованию клиентских приложений: SMB (блок сообщений сервера) и REST. В .NET эти подходы являются абстрактными API `System.IO` и `WindowsAzure.Storage`.

API | Сценарии использования | Примечания
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Требования вашего приложения: <ul><li>Требуется чтение и запись файлов с помощью SMB</li><li>выполнение на устройстве, которое получает доступ к учетной записи службы файлов Azure через порт 445;</li><li>не требуется управлять параметрами администрирования общей папки.</li></ul> | Файловый ввод-вывод, реализованный в службе файлов Azure по протоколу SMB, обычно аналогичен вводу-выводу с любым сетевым файловым ресурсом или локальным устройством хранения. Общие сведения о ряде функций .NET, включая ввод-вывод файлов, см. в руководстве по [консольному приложению](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter) .
[Microsoft. Azure. Storage. File](https://docs.microsoft.com/dotnet/api/overview/azure/storage#client-library) | Требования вашего приложения: <ul><li>Не удается получить доступ к службе файлов Azure по протоколу SMB через порт 445 из-за ограничений брандмауэра или поставщика услуг Интернета.</li><li>требуются административные функции, например возможность задать квоту для общей папки или создать подписанный URL-адрес.</li></ul> | В этой статье показано использование `Microsoft.Azure.Storage.File` для файлового ввода-вывода с помощью функции RESTFUL вместо протокола SMB и управления файловым ресурсом.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Создание консольного приложения и получение сборки

В Visual Studio создайте новое консольное приложение Windows. Ниже показано, как создать консольное приложение в Visual Studio 2019. Эти же действия можно выполнить и в других версиях Visual Studio.

1. Откройте Visual Studio и выберите **Создать проект**.
1. В окне **Создание нового проекта**выберите **консольное приложение (.NET Framework)** для C#, а затем нажмите кнопку **Далее**.
1. В окне **Настройка нового проекта**введите имя приложения и нажмите кнопку **создать**.

Вы можете добавить все примеры кода из этого руководства в метод `Main()` файла `Program.cs` консольного приложения.

Клиентскую библиотеку службы хранилища Azure можно использовать в любом типе приложения .NET. К этим типам относятся облачная служба Azure, веб-приложение, настольные и мобильные приложения. Для упрощения в этом руководстве мы будем использовать консольное приложение.

## <a name="use-nuget-to-install-the-required-packages"></a>Установка необходимых пакетов с помощью NuGet

Чтобы завершить работу с этим руководством, см. Эти пакеты в проекте.

* [Общая библиотека служба хранилища Microsoft Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Этот пакет обеспечивает программный доступ к общим ресурсам в вашей учетной записи хранения.
* [Библиотека BLOB-объектов служба хранилища Microsoft Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Этот пакет обеспечивает программный доступ к ресурсам больших двоичных объектов в вашей учетной записи хранения.
* [Библиотека файлов служба хранилища Microsoft Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Этот пакет обеспечивает программный доступ к файловым ресурсам в вашей учетной записи хранения.
* [Библиотека Configuration Manager Microsoft Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Этот пакет предоставляет класс для синтаксического анализа строки подключения в файле конфигурации независимо от того, где выполняется приложение.

Вы можете использовать NuGet для установки обоих пакетов. Выполните следующие действия.

1. В **Обозреватель решений**щелкните правой кнопкой мыши проект и выберите **Управление пакетами NuGet**.
1. В **диспетчере пакетов NuGet**нажмите кнопку **Обзор**. Затем найдите и выберите **Microsoft. Azure. Storage. BLOB**, а затем нажмите кнопку **установить**.

   На этом шаге выполняется установка пакета и его зависимостей.
1. Найдите и установите такие пакеты:

   * **Microsoft. Azure. Storage. Common**
   * **Microsoft. Azure. Storage. File**
   * **Microsoft. Azure. ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Сохранение учетных данных учетной записи хранения в файле App. config

Затем сохраните свои учетные данные в файле `App.config` проекта. В **Обозреватель решений**дважды щелкните `App.config` и измените файл, чтобы он был похож на следующий пример. Замените `myaccount` именем вашей учетной записи хранения и `mykey` на ключ учетной записи хранения.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> Последняя версия эмулятора хранения Azure не поддерживает службу файлов Azure. Для работы со службой файлов Azure необходимо, чтобы строка подключения указывала на учетную запись хранения Azure в облаке.

## <a name="add-using-directives"></a>Добавление директив using

В **Обозреватель решений**откройте файл `Program.cs` и добавьте следующие директивы using в начало файла.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Доступ к общей папке программным путем

Затем добавьте следующее содержимое в метод `Main()` после приведенного выше кода, чтобы получить строку подключения. Этот код получает ссылку на созданный ранее файл и выводит его содержимое.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Запустите консольное приложение и получите вывод.

## <a name="set-the-maximum-size-for-a-file-share"></a>Установка максимального размера для файлового ресурса

Начиная с версии 5. x клиентской библиотеки службы хранилища Azure, можно задать квоту (максимальный размер) для файлового ресурса. Можно также проверить, какой объем данных хранится в настоящее время в общей папке.

Задание квоты для общего ресурса ограничивает общий размер файлов, хранящихся в общей папке. Если общий размер файлов в общей папке превышает квоту, установленную в общей папке, клиенты не смогут увеличить размер существующих файлов. Клиенты не могут создавать новые файлы, если эти файлы не являются пустыми.

В приведенном ниже примере показано, как проверить текущее использование данных в файловом ресурсе, а также задать для него квоту.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Создание подписи общего доступа для файла или файлового ресурса

Начиная с версии 5.x клиентской библиотеки хранилища Azure можно создать подпись общего доступа (SAS) для файлового ресурса или отдельного файла. Также можно создать политики общего доступа на файловом ресурсе, чтобы управлять подписями общего доступа. Рекомендуется создать политику общего доступа, так как она позволяет отозвать SAS, если она будет скомпрометирована.

В следующем примере создается политика общего доступа для общей папки. В примере эта политика используется для предоставления ограничений для SAS в файле в общей папке.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Дополнительные сведения о создании и использовании подписанных URL-отправок см. [в статье как работает подпись общего доступа](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Копирование файлов

Начиная с версии 5.x клиентской библиотеки хранилища Azure можно скопировать файл в другой файл, файл в большой двоичный объект или BLOB-объект в файл. В следующих разделах мы покажем, как выполнять эти операции копирования программным способом.

AzCopy также можно использовать для копирования одного файла в другой или для копирования большого двоичного объекта в файл или наоборот. См. подробнее о [начале работы с AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> При копировании большого двоичного объекта в файл или файла в большой двоичный объект необходимо использовать подписанный URL-адрес (SAS) для авторизации доступа к исходному объекту, даже если копирование производится внутри одной и той же учетной записи хранения.
>

### <a name="copy-a-file-to-another-file"></a>Копирование файла в другой файл

В приведенном ниже примере файл копируется в другой файл в той же общей папке. Так как эта операция копирования копирует между файлами в одной учетной записи хранения, для копирования можно использовать проверку подлинности с помощью общего ключа.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

### <a name="copy-a-file-to-a-blob"></a>Копирование файла в большой двоичный объект

В приведенном ниже примере файл создается и копируется в большой двоичный объект в пределах одной и той же учетной записи хранения. В примере для исходного файла создается SAS, который служба использует для авторизации доступа к исходному файлу во время операции копирования.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Таким же образом можно скопировать BLOB-объект в файл. Если исходным объектом является большой двоичный объект, создайте SAS для авторизации доступа к этому объекту во время операции копирования.

## <a name="share-snapshots"></a>Моментальные снимки общих ресурсов

Начиная с версии 8.5 клиентской библиотеки службы хранилища Azure, можно создавать моментальные снимки общих ресурсов. Можно также получить список моментальных снимков общих ресурсов, просмотреть и удалить их. Моментальные снимки общих ресурсов доступны только для чтения, поэтому для них запрещены операции записи.

### <a name="create-share-snapshots"></a>Создание моментальных снимков общих ресурсов

В следующем примере создается моментальный снимок общего файлового ресурса.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>Вывод списка моментальных снимков общих ресурсов

В следующем примере перечисляются моментальные снимки в общем ресурсе.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>Просмотр файлов и каталогов в моментальных снимках общих ресурсов

В следующем примере показано, как просмотреть файлы и каталоги в моментальных снимках общих ресурсов.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Вывод списка общих папок и моментальных снимков и восстановление общих файловых ресурсов или файлов из моментальных снимков общих ресурсов

Создание снимка общего файлового ресурса позволяет в будущем восстановить отдельные файлы или весь файловый ресурс.

Файл можно восстановить из моментального снимка, сделав запрос на моментальные снимки файлового ресурса. Затем можно получить файл, принадлежащий определенному моментальному снимку общего ресурса. Используйте эту версию для непосредственного чтения и сравнения либо для восстановления.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

### <a name="delete-share-snapshots"></a>Удаление моментальных снимков общих ресурсов

В следующем примере удаляется моментальный снимок общего файлового ресурса.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## Устранение неполадок службы файлов Azure с помощью метрик<a name="troubleshooting-azure-files-using-metrics"></a>

Аналитика службы хранилища Azure теперь поддерживает метрики для службы файлов Azure. Данные метрик позволяют отслеживать запросы и диагностировать проблемы.

Вы можете включить метрики для службы файлов Azure из [портал Azure](https://portal.azure.com). Вы также можете включить метрики программно, вызвав операцию SET FILE Service Properties с REST API или одним из его аналогов в клиентской библиотеке хранилища.

В следующем примере кода показано, как использовать клиентскую библиотеку хранилища для .NET, чтобы включить метрики для службы файлов Azure.

Сначала добавьте следующие директивы `using` в файл `Program.cs` вместе с добавленными выше.

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Хотя большие двоичные объекты Azure, таблицы Azure и очереди Azure используют общий тип `ServiceProperties` в пространстве имен `Microsoft.Azure.Storage.Shared.Protocol`, службы файлов Azure используют собственный тип, тип `FileServiceProperties` в пространстве имен `Microsoft.Azure.Storage.File.Protocol`. Однако для компиляции следующего кода необходимо сослаться на оба пространства имен из кода.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Если возникнут проблемы, см. статью [Устранение неполадок с файлами Azure в Windows](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о службе файлов Azure см. в следующих ресурсах:

### <a name="conceptual-articles-and-videos"></a>Тематические статьи и видео

* [Файлы Azure: удобная облачная файловая система SMB для Windows и Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Использование файлов Azure с Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Средства для работы с хранилищем файлов

* [Get started with AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Начало работы с AzCopy)
* [Использование интерфейса командной строки (CLI) Azure со службой хранилища Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Устранение неполадок службы файлов Azure в Windows](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Ссылка

* [API-интерфейсы службы хранилища Azure для .NET](/dotnet/api/overview/azure/storage)
* [File Service REST API](/rest/api/storageservices/File-Service-REST-API) (API-интерфейс REST файловой службы)

### <a name="blog-posts"></a>Записи блога

* [Общедоступная версия хранилища файлов Azure](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage (Хранилище файлов Azure: взгляд изнутри)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Введение в службу Microsoft Azure Files](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Сохраняемые подключения к файлам Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
