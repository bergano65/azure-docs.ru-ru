---
title: Использование разделов и подписок Служебной шины Azure с Java (Azure-Messaging-servicebus)
description: В этом кратком руководстве описано, как написать код Java с помощью пакета azure-messaging-servicebus для отправки сообщений в раздел Служебной шины Azure и их последующего получения из подписок в этот раздел.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 6a63e907e01174042f679c57e1ef0994ad2a11b9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95909583"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Отправка сообщений в раздел Служебной шины Azure и получение сообщений из подписок в раздел (Java)
В этом кратком руководстве описано, как написать код Java с помощью пакета azure-messaging-servicebus для отправки сообщений в раздел Служебной шины Azure и их последующего получения из подписок в этот раздел.

> [!IMPORTANT]
> Для работы с этим кратким руководством используется новый пакет azure-messaging-servicebus (**предварительная версия**). Чтобы перейти к краткому руководству, в котором используется текущая общедоступная версия (GA) пакета azure-servicebus, см. [Использование очередей Служебной шины Azure с Java для отправки и получения сообщений](service-bus-java-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать [преимущества подписчика Visual Studio или MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) или зарегистрироваться для получения [бесплатной учетной записи](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Выполните шаги из [краткого руководства по созданию раздела Служебной шины и подписок на него с помощью портала Azure](service-bus-quickstart-topics-subscriptions-portal.md). Запишите строку подключения, имя раздела и имя подписки. При работе с этим кратким руководством вы будете использовать только одну подписку. 
- Установите [пакет Azure SDK для][Azure SDK for Java]Java. При использовании Eclipse можно установить [набор средств Azure для Eclipse][Azure Toolkit for Eclipse], включающий в себя пакет Azure SDK для Java. Затем можно добавить **библиотеки Microsoft Azure для Java** в проект. Если вы используете IntelliJ, см. статью [Установка набора средств Azure для IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Отправка сообщений в раздел
В рамках этого раздела вы создадите консольный проект Java и добавите код для отправки сообщений в созданный вами раздел. 

### <a name="create-a-java-console-project"></a>Создание консольного проекта Java
Создайте проект Java с помощью Eclipse или инструмента по своему усмотрению. 

### <a name="configure-your-application-to-use-service-bus"></a>Настройка приложения для использования служебной шины
Добавьте ссылку на библиотеку Служебной шины Azure. Клиентская библиотека Java для служебной шины доступна в [центральном репозитории Maven](https://search.maven.org/search?q=a:azure-messaging-servicebus). Чтобы сослаться на эту библиотеку, используйте следующее объявление зависимостей в файле проекта Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0-beta.7</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>Добавление кода для отправки сообщений в раздел
1. Добавьте в раздел Java-файла следующие инструкции `import`. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Определите переменные для хранения строки подключения и имени раздела в классе, как показано ниже: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Замените `<NAMESPACE CONNECTION STRING>` строкой подключения к пространству имен Служебной шины. Затем замените `<TOPIC NAME>` именем раздела.
3. Добавьте метод с именем `sendMessage` в класс для отправки одного сообщения в раздел. 

    ```java
        static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
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
1. Добавьте метод с именем метод `sendMessageBatch` для отправки сообщений в созданный вами раздел. Этот метод создает `ServiceBusSenderClient` для раздела, вызывает метод `createMessages` для получения списка сообщений, а также подготавливает один или несколько пакетов и отправляет пакеты в раздел. 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
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
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-subscription"></a>Получение сообщений из подписки
В рамках этого раздела вы добавите код для получения сообщений из подписки в раздел. 

1. Добавьте метод с именем `receiveMessages` для получения сообщений из подписки. Этот метод создает `ServiceBusProcessorClient` для подписки, указывая один обработчик для обработки сообщений и другой — для обработки ошибок. Затем он запускает процессор, ждет несколько секунд, выводит полученные сообщения, а затем останавливает и закрывает процессор.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
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
Выполните программу и убедитесь, что выходные данные выглядят примерно так:

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
```

На странице **Обзор** для пространства имен служебной шины в портале Azure можно видеть количество **входящих** и **исходящих** сообщений. Чтобы просмотреть последние значения, возможно, потребуется подождать около минуты, а затем обновить страницу. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Количество входящих и исходящих сообщений" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Перейдите на вкладку **Разделы**, расположенную в средней части панели, и выберите раздел, чтобы просмотреть страницу **Раздел служебной шины** своего раздела. На этой странице вы увидите четыре входящих и четыре исходящих сообщения в диаграмме **Сообщения**. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Экран &quot;Входящие и исходящие сообщения&quot;" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Если вы закомментируете вызов `receiveMessages` в методе `main` и снова запустите приложение, на странице **Раздел служебной шины** будет отображаться 8 входящих сообщений (4 новых), и только четыре исходящих. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Экран &quot;Обновленная страница раздела&quot;" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

При выборе подписки на этой странице вы будете перенаправлены на страницу **Service Bus Subscription** (Подписка служебной шины). Также на этой странице можно увидеть количество активных и недоставленных сообщений и многое другое. Из этого примера видно, что получатель еще не получил четыре активных сообщения. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Экран &quot;Количество активных сообщений&quot;" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими примерами и документацией:

- [Клиентская библиотека Служебной шины Azure для Java: файл сведений](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Примеры на GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Справочник по API Java](https://docs.microsoft.com/java/api/overview/azure/servicebus?view=azure-java-preview&preserve-view=true)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

