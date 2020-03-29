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
ms.openlocfilehash: 4d8be13a75e276d5be6ec71141a13f95601869f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301443"
---
# <a name="develop-for-azure-files-with-net"></a>Разработка для службы файлов Azure с помощью .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

В этом руководстве рассматриваются основы использования .NET для разработки приложений, использующих [службу файлов Azure](storage-files-introduction.md) для хранения данных файлов. Этот учебник создает простое приложение консоли для выполнений основных действий с помощью файлов .NET и Azure:

* Получите содержимое файла.
* Установите максимальный размер или *квоту* для доли файла.
* Создайте общую подпись доступа (ключ SAS) для файла, использующего политику сохраненного доступа, определяемую на элементе общего доступа.
* Скопируйте файл в другой файл в той же учетной записи хранения
* Скопируйте файл в BLOB-объект в той же учетной записи хранения
* Используйте метрики хранения данных Azure для устранения неполадок.

Чтобы узнать больше о файлах Azure, смотрите [Что такое файлы Azure?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Основные сведения об API-интерфейсах .NET

Служба файлов Azure предлагает два широких подхода к использованию клиентских приложений: SMB (блок сообщений сервера) и REST. В рамках .NET эти подходы абстрагируется `System.IO` и `WindowsAzure.Storage` AA.

API | Назначение | Примечания
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Требования вашего приложения: <ul><li>Потребности для чтения/записи файлов с помощью малого и среднего бизнеса</li><li>выполнение на устройстве, которое получает доступ к учетной записи службы файлов Azure через порт 445;</li><li>не требуется управлять параметрами администрирования общей папки.</li></ul> | Файл ввг/оги, реализованный с помощью файлов Azure по SMB, как правило, такой же, как ввих- и огню с любым доле сетевого файла или локальным устройством хранения данных. Для введения в ряд функций в .NET, включая файл ввода/от, [см.](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter)
[Microsoft.Azure.Storage.File](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | Требования вашего приложения: <ul><li>Не увозможность получить доступ к файлам Azure, используя SMB в порту 445 из-за ограничений брандмауэра или интернет-нее</li><li>требуются административные функции, например возможность задать квоту для общей папки или создать подписанный URL-адрес.</li></ul> | Эта статья демонстрирует использование `Microsoft.Azure.Storage.File` файла вв/причин с использованием REST вместо sMB и управления долей файла.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Создание консольного приложения и получение сборки

В Visual Studio создайте новое консольное приложение Windows. Ниже показано, как создать консольное приложение в Visual Studio 2019. Эти же действия можно выполнить и в других версиях Visual Studio.

1. Откройте Visual Studio и выберите **Создать проект**.
1. При **создании нового проекта**выберите **консольное приложение (.NET Framework)** для C, а затем выберите **Следующий**.
1. В **настройке нового проекта**введите имя для приложения и выберите **Create.**

Вы можете добавить все примеры `Main()` кода в этом учебнике к методу файла приложения вашей `Program.cs` консоли.

Клиентскую библиотеку Azure Storage можно использовать в любом типе приложения .NET. К этим типам относятся облачные службы Azure или веб-приложение, а также настольные и мобильные приложения. Для упрощения в этом руководстве мы будем использовать консольное приложение.

## <a name="use-nuget-to-install-the-required-packages"></a>Установка необходимых пакетов с помощью NuGet

Обратитесь к этим пакетам в проекте, чтобы завершить этот учебник:

* [Общая библиотека хранения microsoft Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Этот пакет обеспечивает программный доступ к общим ресурсам в учетной записи хранилища.
* [Библиотека microsoft Azure Storage Blob для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Этот пакет обеспечивает программный доступ к ресурсам blob в вашей учетной записи хранения.
* [Библиотека файлов хранения данных Microsoft Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Этот пакет обеспечивает программный доступ к файловым ресурсам в вашей учетной записи хранения.
* [Библиотека менеджера конфигурации Microsoft Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Этот пакет предоставляет класс для разбора строки соединения в файле конфигурации, где бы ни заражается приложение.

Вы можете использовать NuGet для установки обоих пакетов. Выполните следующие действия.

1. В **Solution Explorer**, правой кнопкой мыши вашего проекта и выбрать Управление **NuGet пакеты**.
1. В **NuGet менеджер пакетов**, выберите **просмотр**. Затем ищите и выбирайте **Microsoft.Azure.Storage.Blob,** а затем выберите **Install.**

   Этот шаг устанавливает пакет и его зависимости.
1. Найдите и установите такие пакеты:

   * **Microsoft.Azure.Storage.Common**
   * **Microsoft.Azure.Storage.File**
   * **Microsoft.Azure.ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Сохраните учетные данные учетной записи хранилища в файле App.config

Затем сохраните учетные данные `App.config` в файле проекта. В **Solution Explorer**— `App.config` дважды щелкните и отспособите файл, чтобы он был похож на следующий пример. Замените `myaccount` имя учетной `mykey` записи хранилища и ключом учетной записи хранилища.

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

В **Solution Explorer** `Program.cs` откройте файл и добавьте следующие директивы в верхнюю часть файла.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Доступ к общей папке программным путем

Затем добавьте следующее `Main()` содержимое в метод, после показанного выше кода, чтобы получить строку соединения. Этот код получает ссылку на файл, который мы создали ранее, и выводит его содержимое.

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

Начиная с версии 5.x библиотеки клиентов хранения azure, можно установить квоту (максимальный размер) для общего файла. Можно также проверить, какой объем данных хранится в настоящее время в общей папке.

Установка квоты на долю ограничивает общий размер файлов, хранящихся на акции. Если общий размер файлов на акции превышает квоту, установленную на акции, клиенты не могут увеличить размер существующих файлов. Клиенты не могут создавать новые файлы, если эти файлы не пусты.

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

Начиная с версии 5.x клиентской библиотеки хранилища Azure можно создать подпись общего доступа (SAS) для файлового ресурса или отдельного файла. Можно также создать сохраненную политику доступа в общей доле файла для управления общими подписями доступа. Мы рекомендуем создать сохраненную политику доступа, поскольку она позволяет отозвать SAS, если она становится скомпрометированной.

Следующий пример создает политику сохраненного доступа на акции. Пример использует эту политику для предоставления ограничений для SAS в файле в доле.

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

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
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

Для получения дополнительной информации о создании и использовании подписей общего доступа [см.](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)

## <a name="copy-files"></a>Копирование файлов

Начиная с версии 5.x клиентской библиотеки хранилища Azure можно скопировать файл в другой файл, файл в большой двоичный объект или BLOB-объект в файл. В следующих разделах мы демонстрируем, как делать эти операции копирования программно.

Вы также можете использовать AzCopy, чтобы скопировать один файл в другой или скопировать каплю в файл или наоборот. См. подробнее о [начале работы с AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> При копировании большого двоичного объекта в файл или файла в большой двоичный объект необходимо использовать подписанный URL-адрес (SAS) для авторизации доступа к исходному объекту, даже если копирование производится внутри одной и той же учетной записи хранения.
>

### <a name="copy-a-file-to-another-file"></a>Копирование файла в другой файл

В приведенном ниже примере файл копируется в другой файл в той же общей папке. Поскольку эта копия операции копируется между файлами в одной учетной записи хранилища, для копирования можно использовать проверку подлинности Shared Key.

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

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Вывод списка общих ресурсов и их моментальных снимков и восстановление общих папок или файлов из моментальных снимков общих ресурсов

Создание снимка общего файлового ресурса позволяет в будущем восстановить отдельные файлы или весь файловый ресурс.

Файл можно восстановить из моментального снимка, сделав запрос на моментальные снимки файлового ресурса. Затем можно получить файл, принадлежащий к определенному моментальному снимку общего обмена. Используйте эту версию для непосредственного чтения и сравнения, либо для восстановления.

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

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Устранение проблем в файлах Azure с помощью метрик<a name="troubleshooting-azure-files-using-metrics"></a>

Аналитика службы хранилища Azure теперь поддерживает метрики для службы файлов Azure. Данные метрик позволяют отслеживать запросы и диагностировать проблемы.

Можно включить метрики для файлов Azure с [портала Azure.](https://portal.azure.com) Вы также можете включить метрики программно, позвонив в службу set File Service Properties с помощью REST API или одного из его аналогов в библиотеке клиентов хранения.

В следующем примере кода показано, как использовать клиентскую библиотеку хранилища для .NET, чтобы включить метрики для службы файлов Azure.

Во-первых, `using` добавьте в `Program.cs` файл следующие директивы, а также те, которые вы добавили выше:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Хотя Azure Blobs, Azure Tables и Azure `ServiceProperties` Queues `Microsoft.Azure.Storage.Shared.Protocol` используют общий тип в пространстве `FileServiceProperties` имен, `Microsoft.Azure.Storage.File.Protocol` Azure Files использует свой собственный тип— тип в пространстве имен. Однако для компиляции следующего кода необходимо ссылаться на оба именных пространства из кода.

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

Если вы столкнулись с какими-либо проблемами, вы можете обратиться к [проблеме Troubleshoot Azure Files в Windows.](storage-troubleshoot-windows-file-connection-problems.md)

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о файлах Azure смотрите следующие ресурсы:

### <a name="conceptual-articles-and-videos"></a>Тематические статьи и видео

* [Файлы Azure: удобная облачная файловая система SMB для Windows и Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Использование Файлов Azure в Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Средства для работы с хранилищем файлов

* [Get started with AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Начало работы с AzCopy)
* [Устранение неполадок службы файлов Azure в Windows](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Справочник

* [API-интерфейсы хранилища Azure для .NET](/dotnet/api/overview/azure/storage)
* [File Service REST API](/rest/api/storageservices/File-Service-REST-API) (API-интерфейс REST файловой службы)

### <a name="blog-posts"></a>Записи блога

* [Хранение файлов Azure, в настоящее время общедоступно](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Внутри хранения файлов Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Представляем службу файлов Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Сохраняемые подключения к файлам Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
