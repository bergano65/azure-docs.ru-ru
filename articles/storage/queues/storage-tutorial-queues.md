---
title: Руководство по работе с очередями хранилища Azure (служба хранилища Azure)
description: В этом руководстве описано, как использовать службы очередей Azure для создания очередей, а также вставки, получения и удаления сообщений.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: cbrooks
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 81d7572f800f191791158f2c1f99e1f072980116
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65151068"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Руководство по Работа с очередями хранилища Azure

Хранилище очередей Azure реализует облачный механизм очередей для поддержки взаимодействия между компонентами распределенного приложения. Каждая очередь отслеживает список сообщений, которые добавляются компонентом отправителя и обрабатываются компонентом получателя. Очередь позволяет мгновенно масштабировать приложение при любом изменении нагрузки. В этой статье описаны основные процессы работы с очередью хранилища Azure.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
>
> - Создание учетной записи хранения Azure
> - Создание приложения
> - Добавление поддержки для асинхронных вызовов
> - Создание очереди
> - Вставка сообщения в очередь
> - Вывод сообщений из очереди
> - Удаление пустой очереди
> - Проверка аргументов командной строки
> - Создание и запуск приложения

## <a name="prerequisites"></a>Предварительные требования

- Получите бесплатную копию межплатформенного редактора [Visual Studio Code](https://code.visualstudio.com/download).
- Скачайте и установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).
- Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

Прежде всего создайте учетную запись хранения Azure. Пошаговые инструкции по созданию учетной записи хранения см. в [этом кратком руководстве](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json).

## <a name="create-the-app"></a>Создание приложения

Создайте приложение .NET Core с именем **QueueApp**. Для простоты одно приложение будет отправлять сообщения в очередь и принимать их.

1. В окне консоли (CMD, PowerShell или Azure CLI) выполните команду `dotnet new`, чтобы создать консольное приложение с именем **QueueApp**. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Перейдите в только что созданную папку **QueueApp**, скомпилируйте приложение и убедитесь, что все прошло правильно.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Должен отобразиться примерно такой результат:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Добавление поддержки для асинхронных вызовов

Так как приложение использует облачные ресурсы, код выполняется асинхронно. Но ключевые слова C# **async** и **await** не поддерживались в методах **Main** вплоть до версии C# 7.1. Вы можете переключиться на подходящий компилятор, указав флаг в файле **csproj**.

1. Перейдите в каталог проекта и введите в командной строке `code .`, чтобы открыть Visual Studio Code в этом каталоге. Оставьте окно командной строки открытым. Позднее нам нужно будет выполнить здесь другие команды. Если появится предложение добавить необходимые для сборки и отладки ресурсы C#, нажмите кнопку **Да**.

2. Откройте файл **QueueApp.csproj** в редакторе.

3. В файле сборки добавьте `<LangVersion>7.1</LangVersion>` в первый элемент **PropertyGroup**. Добавляйте только тег **LangVersion**, поскольку значение **TargetFramework** может быть разным в зависимости от установленной версии .NET.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Сохраните файл **QueueApp.csproj**.

5. Откройте файл исходного кода **Program.cs** и обновите метод **Main**, чтобы он выполнялся асинхронно. Замените **void** возвращаемым значением **async Task**.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Сохраните файл **Program.cs**.

## <a name="create-a-queue"></a>Создание очереди

1. Добавьте пакет **WindowsAzure. Storage** в проект с помощью команды `dotnet add package`. Выполните следующую команду dotnet из папки проекта в окне консоли.

   ```console
   dotnet add package WindowsAzure.Storage
   ```

2. Добавьте в начало файла **Program.cs** следующие пространства имен сразу за инструкцией `using System;`. Это приложение использует типы из добавленных пространств имен, чтобы подключаться к службе хранилища Azure и работать с очередями.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.WindowsAzure.Storage;
   using Microsoft.WindowsAzure.Storage.Queue;
   ```

3. Сохраните файл **Program.cs**.

### <a name="get-your-connection-string"></a>Получение строки подключения

Клиентская библиотека использует строку подключения для создания подключения. Строка подключения доступна в разделе **Параметры** для учетной записи хранения на портале Azure.

1. В веб-браузере откройте [портал Azure](https://portal.azure.com/).

2. Войдите в свою учетную запись хранения на портале Azure.

3. Выберите **Ключи доступа**.

4. Нажмите кнопку **копирования** справа от поля **Строка подключения**.

![Строка подключения](media/storage-tutorial-queues/get-connection-string.png)

Строка подключения имеет такой формат:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Добавление строки подключения в приложение

Добавьте в приложение строку подключения, чтобы оно могло обращаться к учетной записи хранения.

1. Вернитесь к окну Visual Studio Code.

2. В классе **Program** добавьте элемент `private const string connectionString =`, в котором будет размещаться строка подключения.

3. После знака равенства вставьте строковое значение, которое вы скопировали ранее на портале Azure. Значение **ConnectionString** будет уникальным для учетной записи.

4. Удалите код Hello World из метода **Main**. Теперь код будет выглядеть, как показано ниже, но с уникальным значением строки подключения.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. Обновите **Main**, создав объект **CloudQueue** для передачи в методы отправки и получения данных.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Сохраните файл.

## <a name="insert-messages-into-the-queue"></a>Вставка сообщения в очередь

Создайте новый метод для отправки сообщения в очередь. Добавьте следующий метод в класс **Program**. Этот метод получает ссылку на очередь, а если она не существует, создает новую очередь с помощью вызова [CreateIfNotExistsAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.createifnotexistsasync?view=azure-dotnet). Затем он добавляет в очередь сообщение, вызвав [AddMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.addmessageasync?view=azure-dotnet).

1. Добавьте следующий метод **SendMessageAsync** в класс **Program**.

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Сохраните файл.

## <a name="dequeue-messages"></a>Вывод сообщений из очереди

Создайте новый метод с именем **ReceiveMessageAsync**. Этот метод получает сообщение из очереди, вызывая [GetMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.getmessageasync?view=azure-dotnet). Очень важно удалять из очереди успешно полученные сообщения, чтобы они не обрабатывались более одного раза. Получив сообщение, удалите его из очереди с помощью метода [DeleteMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.deletemessageasync?view=azure-dotnet).

1. Добавьте следующий метод **ReceiveMessageAsync** в класс **Program**.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Сохраните файл.

## <a name="delete-an-empty-queue"></a>Удаление пустой очереди

По завершении работы с проектом мы рекомендуем всегда оценивать, нужны ли вам созданные ресурсы. Работающие ресурсы могут означать лишние затраты. Если очередь существует, но пуста, предложите пользователю удалить ее.

1. Дополните метод **ReceiveMessageAsync** запросом на удаление пустой очереди.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Сохраните файл.

## <a name="check-for-command-line-arguments"></a>Проверка аргументов командной строки

Если в приложение переданы аргументы командной строки, мы будем считать их сообщением для добавления в очередь. Объедините эти аргументы в одну строку. Добавьте эту строку в очередь сообщений, вызвав добавленный ранее метод **SendMessageAsync**.

Если в командной строке нет аргументов, выполните операцию извлечения. Вызовите метод **ReceiveMessageAsync**, чтобы получить первое сообщение из очереди.

Перед завершением работы дождитесь ввода пользователя, используя метод **Console.ReadLine**.

1. Дополните метод **Main** проверкой аргументов командной строки и ожиданием действий пользователя.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Сохраните файл.

## <a name="complete-code"></a>Полный код

Ниже приведен полный код для этого проекта.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.WindowsAzure.Storage;
   using Microsoft.WindowsAzure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. В командной строке в каталоге проекта выполните следующую команду dotnet, чтобы скомпилировать проект.

   ```console
   dotnet build
   ```

2. Когда сборка проекта успешно завершится, выполните следующую команду для добавления первого сообщения в очередь.

   ```console
   dotnet run First queue message
   ```

Вы должны увидеть такой результат:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Запустите приложение без аргументов командной строки, чтобы получить и удалить первое сообщение из очереди.

   ```console
   dotnet run
   ```

4. Еще несколько раз выполните приложение, пока не закончатся сообщения в очереди. При следующем запуске вы получите сообщение о том, что очередь пуста, и запрос на ее удаление.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

1. Создание очереди
2. Добавление и удаление сообщений из очереди
3. Удаление очереди службы хранилища Azure

Ознакомьтесь с кратким руководством по очередям Azure, чтобы получить дополнительные сведения.

> [!div class="nextstepaction"]
> [Краткое руководство по очередям](storage-quickstart-queues-portal.md)
