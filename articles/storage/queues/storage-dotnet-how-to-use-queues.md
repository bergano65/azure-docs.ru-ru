---
title: Приступая к работе с хранилищем очередей Azure с помощью .NET — служба хранилища Azure
description: Очереди хранилища обеспечивают надежный асинхронный обмен сообщениями между компонентами приложения. Обмен сообщениями в облаке позволяет масштабировать компоненты приложения независимо друг от друга.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/08/2020
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: afdd9b1b063d0a82c8cdb27ef01b412daaa9f1df
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198905"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Приступая к работе с хранилищем очередей Azure с помощью .NET

## <a name="overview"></a>Обзор

Хранилище очередей Azure — это служба, обеспечивающая обмен сообщениями в облаке между компонентами приложения. При проектировании приложений для масштабирования компоненты приложений часто отделяются, поэтому они могут масштабироваться независимо друг от друга. Хранилище очередей обеспечивает асинхронный обмен сообщениями между компонентами приложения, выполняется ли они в облаке, на рабочем столе, на локальном сервере или на мобильном устройстве. Хранилище очередей также поддерживает управление асинхронными задачами и создание рабочих процессов.

### <a name="about-this-tutorial"></a>О данном учебнике

В этом руководстве показано, как написать код .NET для некоторых распространенных сценариев использования хранилища очередей Azure. Эти сценарии включают создание и удаление очередей, а также добавление, чтение и удаление сообщений.

**Предполагаемое время выполнения:** 45 минут.

### <a name="prerequisites"></a>Предварительные требования

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- [Общая клиентская библиотека службы хранилища Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
- [Клиентская библиотека очереди службы хранилища Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
- [Диспетчер конфигураций Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
- [Учетная запись хранения Azure](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) ;

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

Теперь настройте среду разработки в Visual Studio для работы с примерами кода из этого руководства.

### <a name="create-a-windows-console-application-project"></a>Создание нового проекта консольного приложения Windows

В Visual Studio создайте новое консольное приложение Windows. Ниже показано, как создать консольное приложение в Visual Studio 2019. Эти же действия можно выполнить и в других версиях Visual Studio.

1. Выберите **файл**  >  **создать**  >  **проект**
2. Выбор **платформ**  >  **Windows**
3. Выберите **Консольное приложение (.NET Framework)**.
4. Нажмите кнопку **Далее** .
5. В поле **имя проекта** введите имя приложения.
6. Выберите **Создать**.

Все примеры кода в этом учебнике можно добавить в метод **Main ()** файла **Program.CS** консольного приложения.

Клиентские библиотеки службы хранилища Azure можно использовать в любом типе приложения .NET, включая облачную службу или веб-приложение Azure, а также настольные и мобильные приложения. Для упрощения в этом руководстве мы будем использовать консольное приложение.

### <a name="use-nuget-to-install-the-required-packages"></a>Установка необходимых пакетов с помощью NuGet

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

Чтобы завершить работу с этим руководством, необходимо сослаться на следующие четыре пакета в проекте:

- [Библиотека ядра Azure для .NET](https://www.nuget.org/packages/Azure.Core/): Этот пакет содержит общие примитивы, абстракции и вспомогательные методы для современных клиентских библиотек пакета SDK для .NET.
- [Общая клиентская библиотека службы хранилища Azure для .NET](https://www.nuget.org/packages/Azure.Storage.Common/): Этот пакет предоставляет инфраструктуру, совместно используемую другими клиентскими библиотеками службы хранилища Azure.
- [Библиотека очередей службы хранилища Azure для .NET](https://www.nuget.org/packages/Azure.Storage.Queues/): Этот пакет позволяет работать с служба очередей хранилища Azure для хранения сообщений, к которым клиент может получить доступ.
- [Библиотека Configuration Manager для .NET](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): Этот пакет предоставляет доступ к файлам конфигурации для клиентских приложений.

Для получения этих пакетов можно использовать NuGet. Выполните следующие действия.

1. Щелкните правой кнопкой мыши проект в **Обозреватель решений**и выберите **Управление пакетами NuGet**.
1. Нажмите кнопку **Обзор** .
1. Выполните поиск по запросу "Azure. Storage. Queues" в Интернете и выберите **установить** , чтобы установить клиентскую библиотеку хранилища и ее зависимости. Кроме того, будут установлены библиотеки Azure. Storage. Common и Azure. Core, которые являются зависимостями библиотеки очередей.
1. Выполните поиск по запросу "System. Configuration. ConfigurationManager" в Интернете и нажмите кнопку " **установить** ", чтобы установить Configuration Manager.

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

Чтобы завершить работу с этим руководством, необходимо сослаться на следующие три пакета в проекте:

- [Служба хранилища Microsoft Azure общей клиентской библиотеки для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Этот пакет обеспечивает программный доступ к ресурсам данных в вашей учетной записи хранения.
- [Библиотека очереди служба хранилища Microsoft Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): Эта клиентская библиотека позволяет работать с Служба хранилища Microsoft Azure служба очередей для хранения сообщений, доступ к которым может получить клиент.
- [Библиотека Microsoft Azure Configuration Manager для .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) — этот пакет предоставляет класс для анализа строки подключения в файле конфигурации независимо от среды выполнения приложения.

Для получения этих пакетов можно использовать NuGet. Выполните следующие действия.

1. Щелкните правой кнопкой мыши проект в **Обозреватель решений**и выберите **Управление пакетами NuGet**.
1. Нажмите кнопку **Обзор** .
1. Выполните поиск по запросу "Microsoft. Azure. Storage. Queue" в Интернете и выберите **установить** , чтобы установить клиентскую библиотеку хранилища и ее зависимости. Кроме того, будет установлена библиотека Microsoft. Azure. Storage. Common, которая является зависимостью библиотеки очередей.
1. Выполните поиск по запросу "Microsoft. Azure. ConfigurationManager" в Интернете и выберите **установить** , чтобы установить Configuration Manager Azure.

---

> [!NOTE]
> Пакеты клиентских библиотек хранилища также входят в [пакет Azure SDK для .NET](https://azure.microsoft.com/downloads/). Однако рекомендуется также установить клиентские библиотеки хранилища из NuGet, чтобы обеспечить постоянное наличие последних версий.
>
> Зависимости ODataLib в клиентских библиотеках хранилища для .NET разрешаются с помощью пакетов ODataLib, доступных в NuGet, а не WCF Data Services. Библиотеки ODataLib можно скачать напрямую или указать на них ссылку в проекте через NuGet. Конкретные пакеты ODataLib, используемые клиентскими библиотеками хранилища, — это [OData](https://nuget.org/packages/Microsoft.Data.OData/), [EDM](https://nuget.org/packages/Microsoft.Data.Edm/)и [spatial](https://nuget.org/packages/System.Spatial/). Хотя эти библиотеки используются классами хранилища таблиц Azure, они являются обязательными зависимостями для программирования с помощью клиентских библиотек хранилища.

### <a name="determine-your-target-environment"></a>Определение целевой среды

Примеры из этого руководства можно выполнять в двух средах.

- Вы можете выполнить код в учетной записи хранения Azure в облаке.
- Вы можете выполнить код в эмуляторе хранения Азурите. Азурите — это локальная среда, которая эмулирует учетную запись хранения Azure в облаке. Азурите — это бесплатный вариант для тестирования и отладки кода, пока приложение находится в разработке. Эмулятор использует известную учетную запись и ключ. Дополнительные сведения см. [в статье Использование эмулятора азурите для локальной разработки и тестирования службы хранилища Azure](../common/storage-use-azurite.md).

> [!NOTE]
> Вы можете указать эмулятор хранения, чтобы избежать затрат, связанных с хранилищем Azure. Однако если вы выберете учетную запись хранения Azure в облаке, затраты на выполнение заданий в учебнике будут незначительны.

## <a name="get-your-storage-connection-string"></a>Получение строки подключения к хранилищу

Клиентские библиотеки службы хранилища Azure для .NET поддерживают использование строки подключения к хранилищу для настройки конечных точек и учетных данных для доступа к службам хранилища. См. сведения о том, как [управлять ключами доступа к учетной записи хранения](../common/storage-account-keys-manage.md).

### <a name="copy-your-credentials-from-the-azure-portal"></a>Копирование учетных данных с портала Azure

Чтобы использовать пример кода, вам нужно авторизоваться для получения доступа к своей учетной записи хранения. Для этого предоставьте приложению учетные данные учетной записи хранения в виде строки подключения. Просмотр учетных данных учетной записи хранения:

1. Перейдите к [портал Azure](https://portal.azure.com).
2. Перейдите к учетной записи хранения.
3. В разделе **Параметры** учетной записи хранения выберите параметр **Ключи доступа**. Появятся ключи доступа к учетной записи и полная строка подключения для каждого ключа.
4. Найдите значение **Строка подключения** в разделе **key1** и нажмите кнопку **Скопировать**, чтобы скопировать строку подключения. На следующем этапе вы добавите значение строки подключения в переменную среды.

    ![Снимок экрана, на котором показано, как скопировать строку подключения с портала Azure](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Дополнительные сведения о строках подключения см. в руководстве по [настройке строк подключения службы хранилища Azure](../common/storage-configure-connection-string.md).

> [!NOTE]
> Ключ учетной записи хранения похож на корневой пароль для вашей учетной записи хранения. Не забудьте защитить ключ учетной записи хранения. Не сообщайте его другим пользователям, не определяйте его в коде и не храните его в текстовом файле, доступном другим пользователям. Повторно создайте ключ с помощью портала Azure, если вы считаете, что он мог быть скомпрометирован.

Строку подключения хранилища рекомендуется хранить в файле конфигурации. Чтобы настроить строку подключения, откройте файл *app. config* в Обозреватель решений в Visual Studio. Добавьте содержимое элемента `\<appSettings\>` , показанное ниже. Замените *строку подключения* значением, скопированным из учетной записи хранения на портале.

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

Например, параметр конфигурации может быть приблизительно таким:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Для работы с эмулятором хранения Азурите можно использовать ярлык, сопоставляемый с хорошо известным именем и ключом учетной записи. В этом случае параметр строки подключения будет таким:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Добавление директив using

Добавьте в верхнюю часть файла `Program.cs` следующие директивы `using`:

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-service-client"></a>Создание клиента службы очередей

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

Класс [QueueClient](/dotnet/api/azure.storage.queues.queueclient) позволяет получать очереди, хранящиеся в хранилище очередей. Вот один из способов создать клиента службы.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

Класс [CloudQueueClient](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy) позволяет получать очереди, хранящиеся в хранилище очередей. Вот один из способов создать клиента службы.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Теперь вы можете написать код, который считывает и записывает данные в хранилище очередей.

## <a name="create-a-queue"></a>Создание очереди

В этом примере показано, как создать очередь:

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Вставка сообщения в очередь

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

Чтобы вставить сообщение в существующую очередь, вызовите метод [SendMessage](/dotnet/api/azure.storage.queues.queueclient.sendmessage) . Сообщение может быть либо `string` (в формате UTF-8), либо `byte` массивом. Следующий код создает очередь (если она не существует) и вставляет сообщение:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

Чтобы вставить сообщение в существующую очередь, сначала создайте новый объект [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy). Затем вызовите метод [AddMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy) . `CloudQueueMessage`Можно создать из типа `string` (в формате UTF-8) или `byte` массива. Ниже приведен код, который создает очередь (если она не существует) и вставляет сообщение "Hello, World":

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Просмотр следующего сообщения

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

Вы можете просматривать сообщения в очереди, не удаляя их из очереди, вызывая метод [PeekMessages](/dotnet/api/azure.storage.queues.queueclient.peekmessages) . Если значение параметра *максмессажес* не передается, по умолчанию используется просмотр одного сообщения.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

Можно посмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод [PeekMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy) .

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

---

## <a name="change-the-contents-of-a-queued-message"></a>Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в очередь сообщений и продлевает время ожидания видимости еще на 60 секунд. Это сохраняет состояние работы, связанной с данным сообщением, и позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди без необходимости начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно также сохраняется счетчик повторов. Если количество повторов сообщения превысит *n* раз, его нужно удалить. Это обеспечивает защиту от сообщений, которые инициируют ошибку приложения при каждой попытке обработки.

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

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

---

## <a name="de-queue-the-next-message"></a>Удаление следующего сообщения из очереди

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

Удаление сообщения из очереди в два этапа. При вызове [рецеивемессажес](/dotnet/api/azure.storage.queues.queueclient.receivemessages)вы получаете следующее сообщение в очереди. Сообщение, возвращаемое методом `ReceiveMessages`, становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод [DeleteMessage](/dotnet/api/azure.storage.queues.queueclient.deletemessage). Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывается `DeleteMessage` сразу после обработки сообщения.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

Код удаляет сообщение из очереди в два этапа. При вызове метода [GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy)вы получаете следующее сообщение в очереди. Сообщение, возвращаемое методом `GetMessage`, становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод [DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy). Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывается `DeleteMessage` сразу после обработки сообщения.

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

---

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Использование алгоритма Async-Await со стандартными интерфейсами API хранилища очередей

В этом примере показано использование алгоритма Async-Await со стандартными интерфейсами API хранилища очередей. Вызывается асинхронная версия каждого из методов, на что указывает суффикс *Async* в их названиях. При использовании асинхронного метода алгоритм Async-Await приостанавливает локальное выполнение процесса до завершения вызова. Благодаря этому текущий поток может выполнять другие задачи, что позволяет избежать возникновения узких мест и повысить общую скорость реагирования приложения. Дополнительные сведения об использовании шаблона async-await в .NET см. в разделе [Async и await (C# и Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) .

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

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

---

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Дополнительные параметры для удаления сообщений из очереди

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения.

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

В следующем примере кода метод [рецеивемессажес](/dotnet/api/azure.storage.queues.queueclient.receivemessages) используется для получения 20 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла `foreach`. Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание, что 5 минут начинается для всех сообщений в одно и то же время, поэтому через 5 минут после вызова `ReceiveMessages` , все сообщения, которые не были удалены, снова станут видимыми.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

В следующем примере кода для получения 20 сообщений в одном вызове используется метод « [Messages](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy) ». Затем он обрабатывает каждое сообщение с помощью цикла `foreach`. Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание, что 5 минут начинается для всех сообщений в одно и то же время, поэтому через 5 минут после вызова `GetMessages` , все сообщения, которые не были удалены, снова станут видимыми.

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

---

## <a name="get-the-queue-length"></a>Получение длины очереди

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

Вы можете узнать приблизительное количество сообщений в очереди. Метод [WebMethod](/dotnet/api/azure.storage.queues.queueclient.getproperties) запрашивает у служба очередей получение свойств очереди, включая число сообщений. Свойство [аппроксиматемессажескаунт](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) содержит приблизительное количество сообщений в очереди. Это число не меньше фактического числа сообщений в очереди, но может быть больше.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

Вы можете узнать приблизительное количество сообщений в очереди. Метод [FetchAttributes](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy) отправляет в службу очередей запрос на извлечение атрибутов очереди, включая количество сообщений. Свойство [ApproximateMessageCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy) возвращает последнее значение, полученное `FetchAttributes` методом, без вызова служба очередей.

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
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Удаление очереди

# <a name="net-v12"></a>[\.Чистая 12](#tab/dotnet)

Чтобы удалить очередь и все сообщения в ней, вызовите метод [Delete](/dotnet/api/azure.storage.queues.queueclient.delete) для объекта очереди.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[\.NET версии 11](#tab/dotnetv11)

Чтобы удалить очередь и все сообщения в ней, вызовите метод [Delete](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy) для объекта очереди.

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

---

## <a name="next-steps"></a>Дальнейшие действия

Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе очередей:
  - [Справочник по клиентской библиотеке хранилища для .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  - [Справочник по REST API](https://msdn.microsoft.com/library/azure/dd179355)
- Узнайте, как упростить код, предназначенный для работы со службой хранилища Azure, с помощью [пакета SDK для веб-заданий Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).
- Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  - [Приступая к работе с хранилищем таблиц Azure с помощью .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) .
  - [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../blobs/storage-dotnet-how-to-use-blobs.md) .
  - Информацию о хранении реляционных данных см. в статье [Подключение к базе данных SQL с помощью .NET (C#)](../../sql-database/sql-database-connect-query-dotnet-core.md).

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
