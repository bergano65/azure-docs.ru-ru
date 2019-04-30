---
title: Начало работы с очередями служебной шины Azure | Документация Майкрософт
description: Написание консольного приложения C#, которое использует очереди обмена сообщениями служебной шины.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 22996b277aba96cbbfedbb3e9cc67644d1a535ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714274"
---
# <a name="get-started-with-service-bus-queues"></a>Начало работы с очередями служебной шины
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
В этом руководстве вы создадите консольные приложения .NET Core, чтобы отправлять и получать сообщения из очереди служебной шины. 

## <a name="prerequisites"></a>Технические условия

1. [Visual Studio 2017 с обновлением 3 (версия 15.3, 26730.01)](https://www.visualstudio.com/vs) или более новая версия.
2. [Пакет SDK для .NET Core](https://www.microsoft.com/net/download/windows) версии 2.0 или более новой.
2. Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать ваши [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) или зарегистрироваться для [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
3. Если у вас нет очереди для работы с, выполните шаги [с помощью портала Azure создать очередь служебной шины](service-bus-quickstart-portal.md) статью, чтобы создать очередь.
    1. Чтение быстрого **Обзор** служебной шины **очереди**. 
    2. Чтобы создать служебную шину **пространства имен**. 
    3. Получить **строку подключения**. 
    4. Чтобы создать служебную шину **очереди**. 

## <a name="send-messages-to-the-queue"></a>Отправка сообщений в очередь

Для отправки сообщений в очередь создайте консольное приложение C# с помощью Visual Studio.

### <a name="create-a-console-application"></a>Создание консольного приложение

Откройте Visual Studio и создайте проект **Консольное приложение (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Получение пакета NuGet для служебной шины

1. Щелкните созданный проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.
2. Откройте вкладку **Обзор**, выполните поиск по фразе **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** и выберите элемент **Microsoft.Azure.ServiceBus**. Щелкните **Установить** , чтобы выполнить установку, а затем закройте это диалоговое окно.
   
    ![Установка пакета NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Написание кода для отправки сообщений в очередь

1. В файл Program.cs добавьте следующие операторы `using` в начале определения пространства имен перед объявлением класса.
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. В классе `Program` объявите приведенные ниже переменные. Укажите для переменной `ServiceBusConnectionString` строку подключения, полученную при создании пространства имен, и задайте для `QueueName` имя очереди, которое использовалось при ее создании.
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ``` 

3. Замените содержимое объекта `Main()` по умолчанию следующей строкой кода:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Сразу после `Main()` добавьте следующий асинхронный метод `MainAsync()`, который позволяет вызвать метод отправки сообщений:

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

5. Сразу после метода `MainAsync()` добавьте следующий метод `SendMessagesAsync()`, который позволяет отправлять такое число сообщений, как задано для параметра `numberOfMessagesToSend` (текущее значение — 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
   
6. Вот как будет выглядеть файл Program.cs.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send Messages
                await SendMessagesAsync(numberOfMessages);
                        
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the queue
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the queue
                        await queueClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

7. Запустите программу и перейдите на портал Azure. В окне **Обзор** пространства имен щелкните имя очереди. Отобразится экран очереди **Основное**. Обратите внимание, что значение параметра **Количество активных сообщений** должно равняться **десяти**. Каждый раз при запуске приложения отправителя без получения сообщений (как указано в следующем разделе) это значение увеличивается на 10. А при каждом добавлении сообщения в очередь текущий размер очереди увеличивается, как и значение параметра **Текущий** в колонке **Основное**.
   
      ![Размер сообщения][queue-message]

## <a name="receive-messages-from-the-queue"></a>Получение сообщений из очереди

Чтобы получить отправленных сообщений, создайте другое консольное приложение .NET Core и установить **Microsoft.Azure.ServiceBus** пакет NuGet с инструкциями для предыдущего приложения отправителя.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Написание кода для получения сообщений из очереди

1. В файл Program.cs добавьте следующие операторы `using` в начале определения пространства имен перед объявлением класса.
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. В классе `Program` объявите приведенные ниже переменные. Укажите для переменной `ServiceBusConnectionString` строку подключения, полученную при создании пространства имен, и задайте для `QueueName` имя очереди, которое использовалось при ее создании.
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

3. Замените содержимое объекта `Main()` по умолчанию следующей строкой кода:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Сразу после `Main()` добавьте следующий асинхронный метод `MainAsync()`, который позволяет вызвать метод `RegisterOnMessageHandlerAndReceiveMessages()`.

    ```csharp
    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register the queue message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

5. Сразу после метода `MainAsync()` добавьте следующий метод, который позволяет зарегистрировать обработчик сообщений и получать сообщения, отправленные приложением-отправителем:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

6. Сразу после предыдущего метода добавьте следующий метод `ProcessMessagesAsync()` для обработки полученных сообщений:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Наконец, добавьте следующий метод для обработки исключений, которые могут возникнуть:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    
   
8. Вот как будет выглядеть файл Program.cs.
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register QueueClient's MessageHandler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
                    
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that will process messages
                queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
                await queueClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
                // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
               // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
4. Запустите программу и перейдите на портал еще раз. Обратите внимание, что значения параметра **Количество активных сообщений** и **Текущий** равны **нулю**.
   
    ![Длина очереди][queue-message-receive]

Поздравляем! Теперь вы создали очередь, отправили в нее набор сообщений и получили сообщения из нее.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [примерами в репозитории GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples), демонстрирующими расширенные возможности обмена сообщениями служебной шины.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png

