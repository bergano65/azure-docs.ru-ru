---
title: Краткое руководство. Использование .NET для создания очереди в службе хранилища Azure
description: Из этого краткого руководства вы узнаете, как использовать клиентскую библиотеку службы хранилища Azure для .NET, чтобы создавать очередь и добавлять в нее сообщения, а также читать и обрабатывать сообщения из очереди.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/06/2018
ms.author: tamram
ms.openlocfilehash: b219e1e1e083355f662919fe332725bd9162d390
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885457"
---
# <a name="quickstart-use-net-to-create-a-queue-in-azure-storage"></a>Краткое руководство. Использование .NET для создания очереди в службе хранилища Azure

Из этого краткого руководства вы узнаете, как использовать клиентскую библиотеку службы хранилища Azure для .NET, чтобы создавать очередь и добавлять в нее сообщения, а также читать и обрабатывать сообщения из очереди. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Затем загрузите и установите .NET Core 2.0 для своей ОС. На компьютере Windows можно установить Visual Studio и, если вы предпочитаете платформу .NET Framework, использовать ее. Кроме того, вы можете установить редактор для использования с ОС.

### <a name="windows"></a> Windows

- Установите [.NET Core для Windows](https://www.microsoft.com/net/download/windows) или [.NET Framework](https://www.microsoft.com/net/download/windows) (входит в состав Visual Studio для Windows).
- Установите [Visual Studio для Windows](https://www.visualstudio.com/). Если вы используете .NET Core, устанавливать Visual Studio не обязательно.  

Сведения о выборе между .NET Core и .NET Framework см. в статье [Выбор между .NET Core и .NET Framework для серверных приложений](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

### <a name="linux"></a>Linux

- Установите [.NET Core для Linux](https://www.microsoft.com/net/download/linux).
- При необходимости установите [Visual Studio Code](https://www.visualstudio.com/) и [расширение C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068).

### <a name="macos"></a>macOS

- Установите [.NET Core для macOS](https://www.microsoft.com/net/download/macos).
- При необходимости установите [Visual Studio для Mac](https://www.visualstudio.com/vs/visual-studio-mac/).

## <a name="download-the-sample-application"></a>Загрузка примера приложения

Пример приложения, используемый в этом кратком руководстве, является простым консольным приложением. Пример приложения можно найти на сайте [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

Используйте команду [git](https://git-scm.com/), чтобы скачать копию приложения в среду разработки. 

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Эта команда клонирует репозиторий в локальную папку git. Чтобы открыть решение Visual Studio, найдите папку *storage-queues-dotnet-quickstart*, откройте ее и дважды щелкните файл *storage-queues-dotnet-quickstart.sln*. 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища

Чтобы запустить приложение, укажите строку подключения для учетной записи хранения. Пример приложения считывает строку подключения из переменной среды и использует ее для авторизации запросов к службе хранилища Azure.

После копирования строки подключения запишите ее в переменной среды на локальном компьютере, где выполняется приложение. Чтобы задать переменную среды, откройте окно консоли и следуйте инструкциям для используемой операционной системы. Замените `<yourconnectionstring>` фактической строкой подключения:

### <a name="windows"></a> Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

После добавления переменной среды может потребоваться перезапустить все запущенные приложения, которым может понадобиться считать переменную среды, в том числе окно консоли. Например, если вы используете Visual Studio в качестве редактора, перезапустите Visual Studio перед запуском примера. 

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

После добавления переменной среды запустите `source ~/.bashrc` из окна консоли, чтобы применить изменения.

### <a name="macos"></a>macOS

Измените .bash_profile и добавьте переменную среды.

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

После добавления переменной среды запустите `source .bash_profile` из окна консоли, чтобы применить изменения.

## <a name="run-the-sample"></a>Запуск примера

В этом примере приложения создается очередь и в нее добавляется сообщение. Сначала сообщение просматривается в приложении без удаления из очереди. Затем сообщение извлекается и удаляется из очереди.

### <a name="windows"></a> Windows

Если вы используете Visual Studio в качестве редактора, нажмите клавишу **F5** для запуска. 

Или перейдите к каталогу приложения и запустите приложение с помощью команды `dotnet run`.

```
dotnet run
```

### <a name="linux"></a>Linux

Перейдите к каталогу приложения и запустите приложение с помощью команды `dotnet run`.

```
dotnet run
```

### <a name="macos"></a>macOS

Перейдите к каталогу приложения и запустите приложение с помощью команды `dotnet run`.

```
dotnet run
```

Выходные данные в этом примере приложения будут выглядеть примерно так:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>Разбор примера кода

Изучим пример кода, чтобы понять, как он работает.

### <a name="try-parsing-the-connection-string"></a>Разбор строки подключения

Сначала в примере проверяется, содержит ли переменная среды строку подключения, которую можно проанализировать, чтобы создать объект [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount), указывающий на учетную запись хранения. Чтобы проверить, допустима ли строка подключения, в приложении используется метод [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse). Если результаты выполнения метода **TryParse** успешны, он инициализирует переменную *storageAccount* и возвращает значение **true**.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...    
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>Создание очереди

В примере приложения сначала создается очередь и в нее добавляется сообщение. 

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name 
// is unique in your storage account. 
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Добавление сообщения

Затем сообщение добавляется в конец очереди. 

Сообщение должно быть в формате, допустимом для передачи XML-запросов с кодированием UTF-8. Размер сообщения не должен превышать 64 КБ. Если сообщение содержит двоичные данные, корпорация Майкрософт рекомендует использовать кодировку Base64.

По умолчанию максимальное время действия сообщения составляет 7 дней. Время действия сообщения может быть любым положительным числом. Если указать -1, сообщение будет иметь неограниченное время действия.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

### <a name="peek-a-message-from-the-queue"></a>Просмотр сообщения из очереди

В примере показано, как просмотреть сообщение из очереди. Во время просмотра сообщения можно считывать его содержимое. Но сообщение продолжает отображаться для других клиентов, чтобы другой клиент впоследствии смог извлечь и обработать его.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's 
// visibility, so that another client can still retrieve and process it. 
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Вывод сообщения из очереди

В этом примере также показано, как вывести сообщение из очереди. При выводе сообщения из очереди оно извлекается из начала очереди и временно не отображается для других клиентов. По умолчанию это сообщение остается невидимым в течение 30 секунд. За это время код может обработать сообщение. Чтобы завершить вывод сообщения из очереди, оно удаляется сразу после обработки, чтобы другой клиент не смог вывести из очереди это же сообщение.

Если приложению не удается обработать сообщение из-за сбоя оборудования или программного обеспечения, по завершении указанного периода сообщение снова становится видимым. Другой клиент может получить это же сообщение и повторить попытку.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for 
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Очистка ресурсов

В примере приложения очищаются ресурсы, созданные при удалении очереди. При удалении очереди все сообщения, содержащиеся в ней, также удаляются.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>Ресурсы для разработки приложений .NET с очередями

Ознакомьтесь со следующими дополнительными ресурсами для разработки .NET с использованием хранилища очередей Azure:

### <a name="binaries-and-source-code"></a>Двоичные файлы и исходный код

- Скачайте пакет NuGet для последней версии [клиентской библиотеки .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) для службы хранилища Azure. 
- См. [исходный код клиентской библиотеки .NET](https://github.com/Azure/azure-storage-net) на сайте GitHub.

### <a name="client-library-reference-and-samples"></a>Справочник по клиентской библиотеке и примеры

- Подробные сведения о клиентской библиотеке .NET см. в [справочнике по API-интерфейсу .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Изучите [примеры для хранилища очередей](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues), написанные с использованием клиентской библиотеки .NET.

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как добавлять сообщения в очередь, просматривать их и выводить из очереди, а также обрабатывать сообщения с помощью .NET. 

> [!div class="nextstepaction"]
> [Обмен данными между приложениями с помощью хранилища очередей Azure](https://docs.microsoft.com/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- Дополнительные сведения о .NET Core см. в статье [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/) (Быстрое начало работы с .NET).