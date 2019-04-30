---
title: Экспорт и импорт регистраций концентраторов уведомлений Azure в пакетном режиме | Документация Майкрософт
description: Узнайте, как использовать пакетную поддержку концентраторов уведомлений для выполнения большого числа операций в центре уведомлений или для экспорта всех регистраций.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: jowargo
ms.openlocfilehash: c24fcd5f007b641bb594bb07348491f70c03ea41
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623992"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Экспорт и импорт регистраций концентраторов уведомлений Azure в пакетном режиме
Существуют сценарии, в которых она необходима для создания или изменения большого числа регистраций в концентраторе уведомлений. Некоторые из этих сценариев — это обновления тегов после пакетных вычислений или перенос извещающей реализации для использования концентраторов уведомлений.

В этой статье объясняется, как для выполнения большого числа операций в центре уведомлений или для экспорта всех регистраций в пакетном режиме.

## <a name="high-level-flow"></a>Высокоуровневый поток
Пакетная поддержка обеспечивает для поддержки долго выполняющиеся задания, связанные с миллионами регистраций. Для достижения такого масштаба, поддержка пакетной службы использует службу хранилища Azure для хранения сведений о задании и вывода. Для операций пакетного обновления пользователю требуется для создания файла в контейнер больших двоичных объектов, содержимым которого является списком регистрации операций обновления. При запуске задания, пользователь предоставляет URL-адрес для входного большого двоичного объекта, а также URL-адрес для выходного каталога (а также в контейнер больших двоичных объектов). После запуска задания, пользователь может проверить состояние, опрашивая URL-адрес расположения, предоставленные при запуске задания. Конкретного задания можно выполнять только операции определенного типа (создает, обновляет или удаляет). Операции экспорта выполняются аналогично.

## <a name="import"></a>Импорт

### <a name="set-up"></a>Настройка
В этом разделе предполагается, что у вас есть следующие сущности:

- Провизионированный концентратор уведомлений.
- Контейнер больших двоичных объектов хранилища Azure.
- Ссылки на [пакет NuGet хранилища Azure](https://www.nuget.org/packages/windowsazure.storage/) и [пакета NuGet для центров уведомлений](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.9).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Создание входного файла и сохраните его в большой двоичный объект
Входной файл содержит список регистраций, сериализованных в формате XML, по одному на строку. С помощью пакета SDK для Azure, в следующем примере кода показано, как проводится сериализация регистраций и отправлять их в контейнер больших двоичных объектов.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Предыдущий код сериализует регистрации в памяти и затем передает весь поток в большой двоичный объект. Если вы отправили файл с более чем несколько мегабайт, см. в руководстве BLOB-объектов Azure о том, как выполнять указанные ниже действия. например [блочные BLOB-объекты](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Создание токенов URL-адресов
После передачи входного файла формирования URL-адресов для предоставления центра уведомлений для входного файла и в выходной каталог. Два контейнера в другой BLOB-объекта можно использовать для ввода и вывода.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>отправить задание.
Два входных и выходных URL-адреса можно запустить пакетное задание.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Помимо входных и выходных URL-адреса, в этом примере создается `NotificationHubJob` , содержащий `JobType` объект, который может принимать одно из следующих типов:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

После завершения вызова концентратор уведомлений продолжает выполнение задания и вы можете проверить его состояние с помощью вызова [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

По завершении выполнения задания вы можете проверить результаты, просмотрев следующие файлы в выходном каталоге:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Эти файлы содержат список успешных и неудачных операций из пакета. Формат файла — `.cvs`, в котором каждая строка содержит номер строки исходного файла входных и выходных данных операции (обычно созданная или обновленная регистрации сведения).

### <a name="full-sample-code"></a>Полный пример кода
В следующем примере кода импортирует регистрации в концентраторе уведомлений.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>экспорт.
Регистрация экспорта похожа к импорту, со следующими отличиями:

- Требуется только выходной URL-адрес.
- Вы создадите NotificationHubJob типа ExportRegistrations.

### <a name="sample-code-snippet"></a>Образец фрагмента кода
Ниже приведен образец фрагмента кода для экспорта регистраций на языке Java.

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о регистрации, см. в следующих статьях:

- [Управление регистрацией](notification-hubs-push-notification-registration-management.md)
- [Теги для регистрации](notification-hubs-tags-segment-push-message.md)
- [Регистрация шаблонов](notification-hubs-templates-cross-platform-push-messages.md)
