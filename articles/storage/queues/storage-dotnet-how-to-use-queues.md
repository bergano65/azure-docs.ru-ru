---
title: Начало работы с хранилищем очередей Azure с помощью .NET — хранилище Azure
description: Очереди хранилища обеспечивают надежный асинхронный обмен сообщениями между компонентами приложения. Обмен сообщениями в облаке позволяет масштабировать компоненты приложения независимо друг от друга.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 44f1953d01f827db1cbb65f9029c62569425745e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077050"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Приступая к работе с хранилищем очередей Azure с помощью .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Обзор

Хранилище очередей Azure — это служба, обеспечивающая обмен сообщениями в облаке между компонентами приложения. При разработке приложений для масштабирования компоненты приложения часто не связаны между собой, так что они могут масштабироваться независимо друг от друга. Хранилище очередей обеспечивает асинхронный обмен сообщениями для взаимодействия между компонентами приложения независимо от того, где они выполняются: в облаке, на рабочем столе, локальном сервере или мобильном устройстве. Хранилище очередей также поддерживает управление асинхронными задачами и создание рабочих процессов.

### <a name="about-this-tutorial"></a>О данном учебнике

В этом руководстве показано, как написать код .NET для некоторых распространенных сценариев использования хранилища очередей Azure. Эти сценарии включают создание и удаление очередей, а также добавление, чтение и удаление сообщений.

**Предполагаемое время выполнения:** 45 минут

### <a name="prerequisites"></a>Технические условия

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Распространенные клиентская библиотека хранилища Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
* [Клиентская библиотека Azure очереди службы хранилища для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Диспетчер конфигураций Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
* [учетная запись хранения Azure](../common/storage-quickstart-create-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

Теперь настройте среду разработки в Visual Studio для работы с примерами кода из этого руководства.

### <a name="create-a-windows-console-application-project"></a>Создание нового проекта консольного приложения Windows

В Visual Studio создайте новое консольное приложение Windows. Ниже показано, как создать консольное приложение в Visual Studio 2019. Эти же действия можно выполнить и в других версиях Visual Studio.

1. Выберите **Файл** > **Создать** > **Проект**.
2. Выберите **платформы** > **Windows**
3. Выберите **Консольное приложение (.NET Framework)** .
4. Щелкните **Далее**.
5. В **имя_проекта** введите имя для вашего приложения
6. Нажмите кнопку **Создать**

Все примеры кода в этом руководстве могут добавляться к **Main()** метод в приложении консоли **Program.cs** файл.

Можно использовать клиентские библиотеки службы хранилища Azure в любой тип приложения .NET, включая Azure облачной службы или веб-приложение, и для настольных и мобильных приложений. Для упрощения в этом руководстве мы будем использовать консольное приложение.

### <a name="use-nuget-to-install-the-required-packages"></a>Установка необходимых пакетов с помощью NuGet

Необходимо сослаться на следующие три пакета в проекте для работы с этим руководством:

* [Microsoft Common клиентская библиотека хранилища Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): этот пакет предоставляет программный доступ к ресурсам данных в вашей учетной записи хранения.
* [Библиотека хранилища очереди Microsoft Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): Эта клиентская библиотека позволяет работать с облачной службой очереди службы хранилища Microsoft Azure для хранения сообщений, которые может использовать клиент.
* [Библиотека Microsoft Azure Configuration Manager для .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/). Этот пакет предоставляет класс для анализа строки подключения в файле конфигурации независимо от среды выполнения приложения.

Чтобы получить эти пакеты можно использовать NuGet. Выполните следующие действия.

1. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**.
2. Щелкните **Обзор**.
3. Поиск в Интернете «Microsoft.Azure.Storage.Queue» и выберите **установить** установить клиентскую библиотеку службы хранилища и его зависимости. Также будут установлены Microsoft.Azure.Storage.Common библиотеку, которая является зависимостью библиотеки очереди.
4. Поиск в Интернете «Microsoft.Azure.ConfigurationManager» и выберите **установить** установить Azure Configuration Manager.

> [!NOTE]
> Пакеты библиотеки клиента хранения также включены в [Azure SDK для .NET](https://azure.microsoft.com/downloads/). Тем не менее рекомендуется также установить клиентские библиотеки хранилища из NuGet, чтобы всегда иметь последние версии.
>
> Зависимости ODataLib в клиентских библиотек службы хранилища для .NET разрешаются с помощью пакетов ODataLib, доступных в NuGet, а не в WCF Data Services. Библиотеки ODataLib можно скачать напрямую или указать на них ссылку в проекте через NuGet. К специальным пакетам ODataLib, используемых клиентских библиотек хранилища, [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/), и [пространственный](http://nuget.org/packages/System.Spatial/). Хотя эти библиотеки используются классами хранилища таблиц Azure, они являются обязательными зависимостями для программирования с использованием клиентских библиотек хранилища.

### <a name="determine-your-target-environment"></a>Определение целевой среды

Примеры из этого руководства можно выполнять в двух средах.

* Вы можете выполнить код в учетной записи хранения Azure в облаке.
* Вы можете выполнить код в эмуляторе хранения Azure. Эмулятор хранения — это локальная среда, эмулирующая учетную запись хранения Azure в облаке. Эмулятор можно использовать как бесплатный вариант для тестирования и отладки кода, пока приложение находится на стадии разработки. Эмулятор использует известную учетную запись и ключ. Дополнительные сведения см. в руководстве по [использованию эмулятора хранения Azure для разработки и тестирования](../common/storage-use-emulator.md).

Выбрав учетную запись хранения в облаке, скопируйте первичный ключ доступа к этой учетной записи хранения с портала Azure. Дополнительные сведения см. в разделе о [ключах доступа](../common/storage-account-manage.md#access-keys).

> [!NOTE]
> Вы можете указать эмулятор хранения, чтобы избежать затрат, связанных с хранилищем Azure. Однако если вы выберете учетную запись хранения Azure в облаке, затраты на выполнение заданий в учебнике будут незначительны.

### <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища

Клиентские библиотеки хранилища Azure для поддержки .NET, используя строку подключения хранилища для настройки конечных точек и учетные данные для доступа к службам хранилища. Строку подключения хранилища рекомендуется хранить в файле конфигурации.

Дополнительные сведения о строках подключения см. в руководстве по [настройке строк подключения службы хранилища Azure](../common/storage-configure-connection-string.md).

> [!NOTE]
> Ключ учетной записи хранения похож на корневой пароль для вашей учетной записи хранения. Не забудьте защитить ключ учетной записи хранения. Не сообщайте его другим пользователям, не определяйте его в коде и не храните его в текстовом файле, доступном другим пользователям. Повторно создайте ключ с помощью портала Azure, если вы считаете, что он мог быть скомпрометирован.

Чтобы настроить строку подключения, откройте **app.config** файл из обозревателя решений в Visual Studio. Добавить содержимое **\<appSettings\>** элемент, показанный ниже. Замените *имя учетной записи* с именем вашей учетной записи хранения и *ключ учетной записи* ключом доступа своей учетной записи:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Например, параметр конфигурации может быть приблизительно таким:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Чтобы указать эмулятор хранения, можно использовать ярлык, который сопоставляется с хорошо известным именем и ключом. В этом случае параметр строки подключения будет таким:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Добавление директив using

Добавьте в верхнюю часть файла `Program.cs` следующие директивы `using`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Копирование учетных данных с портала Azure

Чтобы использовать пример кода, вам нужно авторизоваться для получения доступа к своей учетной записи хранения. Для этого предоставьте приложению учетные данные учетной записи хранения в виде строки подключения. Просмотр учетных данных учетной записи хранения:

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Перейдите к учетной записи хранения.
3. В разделе **Параметры** учетной записи хранения выберите параметр **Ключи доступа**. Появятся ключи доступа к учетной записи и полная строка подключения для каждого ключа.
4. Найдите значение **Строка подключения** в разделе **key1** и нажмите кнопку **Скопировать**, чтобы скопировать строку подключения. На следующем этапе вы добавите значение строки подключения в переменную среды.

    ![Снимок экрана, на котором показано, как скопировать строку подключения с портала Azure](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

### <a name="parse-the-connection-string"></a>Проанализируйте строку подключения

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Создание клиента службы очередей

Класс [CloudQueueClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet) позволяет получать очереди, хранящиеся в хранилище очередей. Вот один из способов создать клиента службы.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

Теперь вы можете написать код, который считывает и записывает данные в хранилище очередей.

## <a name="create-a-queue"></a>Создание очереди

В этом примере показано, как создать очередь, если она не существует:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Вставка сообщения в очередь

Чтобы вставить сообщение в существующую очередь, сначала создайте новый объект [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet). Затем вызовите метод [AddMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet). Для создания объекта **CloudQueueMessage** можно использовать строку (в формате UTF-8) или массив **байтов**. Ниже приведен код, который создает очередь (если она отсутствует) и вставляет сообщение "Hello World".

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>Просмотр следующего сообщения

Просмотреть сообщение в начале очереди, не удаляя его, можно с помощью метода [PeekMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet) .

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

## <a name="change-the-contents-of-a-queued-message"></a>Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в очередь сообщений и продлевает время ожидания видимости еще на 60 секунд. Это сохраняет состояние работы, связанной с данным сообщением, и позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди без необходимости начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно также сохраняется счетчик повторов. Если количество повторов сообщения превысит *n* раз, его нужно удалить. Это обеспечивает защиту от сообщений, которые инициируют ошибку приложения при каждой попытке обработки.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

## <a name="de-queue-the-next-message"></a>Удаление следующего сообщения из очереди

Код удаляет сообщение из очереди в два этапа. При вызове метода [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet)вы получаете следующее сообщение в очереди. Сообщение, возвращаемое методом **GetMessage** , становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод [DeleteMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet). Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **DeleteMessage** сразу после обработки сообщения.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Использование алгоритма Async-Await со стандартными интерфейсами API хранилища очередей

В этом примере показано использование алгоритма Async-Await со стандартными интерфейсами API хранилища очередей. Вызывается асинхронная версия каждого из методов, на что указывает суффикс *Async* в их названиях. При использовании асинхронного метода алгоритм Async-Await приостанавливает локальное выполнение процесса до завершения вызова. Благодаря этому текущий поток может выполнять другие задачи, что позволяет избежать возникновения узких мест и повысить общую скорость реагирования приложения. Дополнительные сведения об использовании алгоритма Async-Await в .NET см. в статье [Асинхронное программирование с использованием ключевых слов Async и Await (C# и Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Дополнительные параметры для удаления сообщений из очереди

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод [GetMessages](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet) используется для получения 20 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла **foreach** . Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание на то, что пятиминутный период начинается для всех сообщений одновременно, поэтому по прошествии пяти минут с момента вызова **GetMessages**все сообщения, которые не были удалены, снова становятся видимыми.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод [FetchAttributes](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet) отправляет в службу очередей запрос на извлечение атрибутов очереди, включая количество сообщений. Свойство [ApproximateMessageCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet) возвращает последнее значение, полученное с использованием метода **FetchAttributes**, без обращения к службе очередей.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>Удаление очереди

Чтобы удалить очередь и все сообщения в ней, вызовите метод [Delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet) для объекта очереди.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

## <a name="next-steps"></a>Дальнейшие действия

Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

* Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе очередей:
  * [Справочник по клиентской библиотеке хранилища для .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [Справочник по REST API](https://msdn.microsoft.com/library/azure/dd179355)
* Узнайте, как упростить код, предназначенный для работы со службой хранилища Azure, с помощью [пакета SDK для веб-заданий Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).
* Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  * [Приступая к работе с хранилищем таблиц Azure с помощью .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) .
  * [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../blobs/storage-dotnet-how-to-use-blobs.md) .
  * Информацию о хранении реляционных данных см. в статье [Подключение к базе данных SQL с помощью .NET (C#)](../../sql-database/sql-database-connect-query-dotnet-core.md).

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
