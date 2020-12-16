---
title: Использование очередей служебной шины Azure с Java (azure-messaging-servicebus)
description: В этом руководстве показано, как использовать Java для отправки и получения сообщений через очередь Служебной шины Azure. Вы используете новый пакет azure-messaging-servicebus.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4eae72b38d36348a6bdc98d3cbd98a5a712b3161
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97399304"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Отправка сообщений в очереди Служебной шины и получение сообщений из них (Java)
В этом кратком руководстве описывается создание приложения Java для отправки сообщений в очередь служебной шины Azure и получения сообщений из нее. 

> [!IMPORTANT]
> Для работы с этим кратким руководством используется новый пакет azure-messaging-servicebus. Сведения об использовании пакета прежней версии см. в кратком руководстве по [отправке и получению сообщений с помощью azure-servicebus](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Предварительные требования
- Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать [преимущества подписчика MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) или зарегистрироваться для получения [бесплатной учетной записи](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Если у вас нет подходящей очереди служебной шины, [создайте ее с помощью портала Azure](service-bus-quickstart-portal.md). Запишите **строку подключения** к пространству имен Служебной шины и имя созданной **очереди**.
- Установите [пакет Azure SDK для][Azure SDK for Java]Java. При использовании Eclipse можно установить [набор средств Azure для Eclipse][Azure Toolkit for Eclipse], включающий в себя пакет Azure SDK для Java. Затем можно добавить **библиотеки Microsoft Azure для Java** в проект. Если вы используете IntelliJ, см. статью [Установка набора средств Azure для IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Отправка сообщений в очередь
В рамках этого раздела вы создадите консольный проект Java и добавите код для отправки сообщений в созданную вами ранее очередь. 

### <a name="create-a-java-console-project"></a>Создание консольного проекта Java
Создайте проект Java с помощью Eclipse или инструмента по своему усмотрению. 

### <a name="configure-your-application-to-use-service-bus"></a>Настройка приложения для использования служебной шины
Добавьте ссылку на библиотеку служебной шины Azure. Клиентская библиотека Java для служебной шины доступна в [Центральном репозитории Maven](https://search.maven.org/search?q=a:azure-messaging-servicebus). Чтобы сослаться на эту библиотеку, используйте следующее объявление зависимостей в файле проекта Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Добавление кода для отправки сообщений в очередь
1. Добавьте в раздел Java-файла следующие инструкции `import`. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. В классе, определите переменные для хранения строки подключения и имени очереди, как показано ниже. 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Замените `<NAMESPACE CONNECTION STRING>` строкой подключения к пространству имен Служебной шины. Затем замените `<QUEUE NAME>` именем очереди.
3. Добавьте метод с именем `sendMessage`, в класс для отправки одного сообщения в очередь. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. Добавьте метод с именем `createMessages` в класс, чтобы создать список сообщений. Как правило, эти сообщения поступают из различных частей приложения. Здесь мы создадим список примеров сообщений.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Добавьте метод с именем `sendMessageBatch` для отправки сообщений в созданную вами очередь. Этот метод создает `ServiceBusSenderClient` для очереди, вызывает метод `createMessages` для получения списка сообщений, подготавливает один или несколько пакетов и отправляет пакеты в очередь. 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-queue"></a>Получение сообщений из очереди
В рамках этого раздела вы добавите код для получения сообщений из очереди. 

1. Добавьте метод с именем `receiveMessages` для получения сообщений из очереди. Этот метод создает `ServiceBusProcessorClient` для очереди, указывая один обработчик для обработки сообщений и другой — для обработки ошибок. Затем он запускает процессор, ждет несколько секунд, выводит полученные сообщения, а затем останавливает и закрывает процессор.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString());
        };

        // handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }    
    ```
2. Обновите метод `main`, чтобы вызвать методы `sendMessage`, `sendMessageBatch` и `receiveMessages` и вызвать `InterruptedException`.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Запустите приложение
При запуске приложения в окне консоли отображаются следующие сообщения. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Received message: Hello, World!
Received message: First message in the batch
Received message: Second message in the batch
Received message: Three message in the batch
Stopping and closing the processor
```

На странице **Обзор** для пространства имен служебной шины в портале Azure можно видеть количество **входящих** и **исходящих** сообщений. Чтобы просмотреть последние значения, возможно, потребуется подождать около минуты, а затем обновить страницу. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Количество входящих и исходящих сообщений" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Выберите очередь на этой странице **Обзор**, чтобы перейти на страницу **Очередь служебной шины**. На этой странице отображаются количество **входящих** и **исходящих** сообщений. Вы также можете увидеть другие сведения, например **текущий размер** очереди, **максимальный размер**, **количество активных сообщений** и т. д. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Сведения об очереди" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Next Steps
Ознакомьтесь со следующими примерами и документацией:

- [Клиентская библиотека Служебной шины Azure для Java: файл сведений](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Примеры на GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Справочник по API Java](/java/api/overview/azure/servicebus?preserve-view=true&view=azure-java-stable)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
