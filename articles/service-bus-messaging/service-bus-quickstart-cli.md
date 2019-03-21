---
title: Краткое руководство. Создание очереди Служебной шины Azure с помощью Azure CLI | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как с помощью Azure CLI создать очередь Служебной шины Azure. Затем вы отправите сообщения в очередь и получите сообщения из нее, используя пример приложения Java.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: java
ms.topic: quickstart
ms.date: 02/25/2019
ms.author: spelluru
ms.openlocfilehash: b40cd130039f7d55b69198b4be4af908dedbe8b7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076893"
---
# <a name="quickstart-use-the-azure-cli-to-create-a-service-bus-queue"></a>Краткое руководство. Создание очереди служебной шины с помощью Azure CLI

Служебная шина Microsoft Azure представляет собой брокер интеграции сообщений корпоративного уровня, который гарантирует защищенную и надежную доставку сообщений. Типичный сценарий применения служебной шины подразумевает функциональное разделение двух или более приложений, служб либо процессов, позволяя им обмениваться данными или сведениями о состоянии, а также отправлять друг другу сообщения. При этом приложениям не обязательно подключаться к сети одновременно. 

Например, розничная компания может отправлять данные из точек продаж во вспомогательный офис или региональный центр дистрибуции для пополнения запасов и сбора сведений о товарах. В этом примере клиентское приложение отправляет сообщения в очередь Служебной шины Azure и получает сообщения из нее.

![очередь](./media/service-bus-quickstart-cli/quick-start-queue.png)

В этом кратком руководстве объясняется, как отправлять и принимать сообщения через Служебную шину с помощью Azure CLI и библиотеки Java для Служебной шины. И наконец, если вас интересует более подробная техническая информация, изучите [это описание](#understand-the-sample-code) ключевых элементов нашего примера кода.

Если у вас еще нет подписки Azure, вы можете создать [бесплатная учетная запись][], прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure

Нажмите кнопку Cloud Shell в меню в правом верхнем углу портала Azure, а затем в раскрывающемся списке **Выбрать окружение** щелкните пункт **Bash**. 

## <a name="use-the-azure-cli-to-create-resources"></a>Создание ресурсов с помощью Azure CLI

В Cloud Shell введите следующие команды в командной строке Bash, чтобы подготовить ресурсы служебной шины. Не забудьте заменить все заполнители правильными значениями: Пример программы Java ожидает имя очереди BasicQueue, поэтому не меняйте его. 

```azurecli-interactive
# Create a resource group
resourceGroupName="myResourceGroup"

az group create --name $resourceGroupName --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create --resource-group $resourceGroupName --name $namespaceName --location eastus

# Create a Service Bus queue
az servicebus queue create --resource-group $resourceGroupName --namespace-name $namespaceName --name BasicQueue

# Get the connection string for the namespace
connectionString=$(az servicebus namespace authorization-rule keys list --resource-group $resourceGroupName --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv)
```

После выполнения последней команды скопируйте строку подключения и имя очереди, а затем вставьте их в любое временное расположение, например в окно Блокнота. Они понадобятся вам на следующем шаге.

## <a name="send-and-receive-messages"></a>Отправка и получение сообщений

Создав пространство имен и очередь и получив необходимые учетные данные, вы можете приступать к отправке и получению сообщений. Этот код можно изучить в [папке с примером на GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

1. Выполните следующую команду, которая клонирует [репозиторий GitHub для служебной шины](https://github.com/Azure/azure-service-bus/) на компьютере:

   ```bash
   git clone https://github.com/Azure/azure-service-bus.git
   ```

1. Выберите папку примера в качестве текущего каталога, используя символы косой черты в качестве разделителей пути:

   ```bash
   cd azure-service-bus/samples/Java/azure-servicebus/QueuesGettingStarted
   ```

1. Запустите сборку приложения с помощью следующей команды:
   
   ```bash
   mvn clean package -DskipTests
   ```

1. Чтобы запустить программу, выполните следующую команду, заменив строку подключения скопированным ранее значением:

   ```bash
   java -jar ./target/queuesgettingstarted-1.0.0-jar-with-dependencies.jar -c "<SERVICE BUS NAMESPACE CONNECTION STRING>" 
   ```

1. Проследите, как в очередь отправляются 10 сообщений. Сохранение порядка сообщений не гарантируется, но вы можете отслеживать отправку, подтверждение и получение сообщений с полезными данными.

    ```
    Message sending: Id = 0
    Message sending: Id = 1
    Message sending: Id = 2
    Message sending: Id = 3
    Message sending: Id = 4
    Message sending: Id = 5
    Message sending: Id = 6
    Message sending: Id = 7
    Message sending: Id = 8
    Message sending: Id = 9
            Message acknowledged: Id = 9
            Message acknowledged: Id = 3
                                    Message received:
                                                    MessageId = 9,
                                                    SequenceNumber = 54324670505156609,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.972Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.972Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Nikolaus, name = Kopernikus ]
    
            Message acknowledged: Id = 2
            Message acknowledged: Id = 5
            Message acknowledged: Id = 1
            Message acknowledged: Id = 8
            Message acknowledged: Id = 7
            Message acknowledged: Id = 0
            Message acknowledged: Id = 6
            Message acknowledged: Id = 4
                                    Message received:
                                                    MessageId = 3,
                                                    SequenceNumber = 58828270132527105,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.972Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.972Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Steven, name = Hawking ]
    
                                    Message received:
                                                    MessageId = 2,
                                                    SequenceNumber = 9288674231451649,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.012Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.012Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Marie, name = Curie ]
    
                                    Message received:
                                                    MessageId = 1,
                                                    SequenceNumber = 22799473113563137,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.025Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.025Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Werner, name = Heisenberg ]
    
                                    Message received:
                                                    MessageId = 8,
                                                    SequenceNumber = 67835469387268097,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.028Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.028Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Johannes, name = Kepler ]
    
                                    Message received:
                                                    MessageId = 7,
                                                    SequenceNumber = 4785074604081153,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.020Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.020Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Galileo, name = Galilei ]
    
                                    Message received:
                                                    MessageId = 5,
                                                    SequenceNumber = 13792273858822145,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.027Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.027Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Niels, name = Bohr ]
    
                                    Message received:
                                                    MessageId = 0,
                                                    SequenceNumber = 18295873486192641,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.021Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.021Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Albert, name = Einstein ]
    
                                    Message received:
                                                    MessageId = 6,
                                                    SequenceNumber = 281474976710657,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.019Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.019Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Michael, name = Faraday ]
    
                                    Message received:
                                                    MessageId = 4,
                                                    SequenceNumber = 63331869759897601,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.964Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.964Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Isaac, name = Newton ]
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

В Azure Cloud Shell выполните следующую команду, чтобы удалить группу ресурсов, пространство имен и все связанные с ними ресурсы:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="understand-the-sample-code"></a>Разбор примера кода

Этот раздел содержит дополнительные сведения о ключевых фрагментах нашего примера кода. Вы можете просмотреть этот код в репозитории GitHub [здесь](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

### <a name="get-connection-string"></a>Получение строки подключения

Метод runApp считывает значение строки подключения из аргументов и передает их в программу. 

```java
public static void main(String[] args) {

    System.exit(runApp(args, (connectionString) -> {
        QueuesGettingStarted app = new QueuesGettingStarted();
        try {
            app.run(connectionString);
            return 0;
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 1;
        }
    }));
}

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
```

### <a name="create-queue-clients-to-send-and-receive"></a>Создание клиентов очереди для отправки и получения

Для отправки и получения сообщений метод `run()` создает экземпляры клиентов очереди, используя значения строки подключения и имени очереди. Этот код создает два клиента очереди: один — для отправки, другой — для получения:

```java
public void run(String connectionString) throws Exception {

    // Create a QueueClient instance for receiving using the connection string builder
    // We set the receive mode to "PeekLock", meaning the message is delivered
    // under a lock and must be acknowledged ("completed") to be removed from the queue
    QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(connectionString, "BasicQueue"), ReceiveMode.PEEKLOCK);
    // We are using single thread executor as we are only processing one message at a time
    ExecutorService executorService = Executors.newSingleThreadExecutor();
    this.registerReceiver(receiveClient, executorService);

    // Create a QueueClient instance for sending and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(connectionString, "BasicQueue"), ReceiveMode.PEEKLOCK);
    this.sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    // wait for ENTER or 10 seconds elapsing
    waitForEnter(10);

    // shut down receiver to close the receive loop
    receiveClient.close();
    executorService.shutdown();
}
``` 

### <a name="construct-and-send-messages"></a>Создание и отправка сообщений

Метод `sendMessagesAsync()` создает набор из 10 сообщений и асинхронно отправляет их с помощью клиента очереди:

```java
CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
    List<HashMap<String, String>> data =
            GSON.fromJson(
                    "[" +
                            "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                            "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                            "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                            "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                            "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                            "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                            "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                            "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                            "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                            "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                            "]",
                    new TypeToken<List<HashMap<String, String>>>() {}.getType());

    List<CompletableFuture> tasks = new ArrayList<>();
    for (int i = 0; i < data.size(); i++) {
        final String messageId = Integer.toString(i);
        Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
        message.setContentType("application/json");
        message.setLabel("Scientist");
        message.setMessageId(messageId);
        message.setTimeToLive(Duration.ofMinutes(2));
        System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
        tasks.add(
                sendClient.sendAsync(message).thenRunAsync(() -> {
                    System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                }));
    }
    return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
}
```

### <a name="receive-messages"></a>Получение сообщений

Метод `registerReceiver()` регистрирует обратный вызов `RegisterMessageHandler` и настаивает некоторые параметры обработчика сообщений:

```java
void registerReceiver(QueueClient queueClient, ExecutorService executorService) throws Exception {

    
    // register the RegisterMessageHandler callback with executor service
    queueClient.registerMessageHandler(new IMessageHandler() {
                                            // callback invoked when the message handler loop has obtained a message
                                            public CompletableFuture<Void> onMessageAsync(IMessage message) {
                                                // receives message is passed to callback
                                                if (message.getLabel() != null &&
                                                        message.getContentType() != null &&
                                                        message.getLabel().contentEquals("Scientist") &&
                                                        message.getContentType().contentEquals("application/json")) {

                                                    byte[] body = message.getBody();
                                                    Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                                                    System.out.printf(
                                                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                                                                    "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                                                            message.getMessageId(),
                                                            message.getSequenceNumber(),
                                                            message.getEnqueuedTimeUtc(),
                                                            message.getExpiresAtUtc(),
                                                            message.getContentType(),
                                                            scientist != null ? scientist.get("firstName") : "",
                                                            scientist != null ? scientist.get("name") : "");
                                                }
                                                return CompletableFuture.completedFuture(null);
                                            }

                                            // callback invoked when the message handler has an exception to report
                                            public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                                                System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                                            }
                                        },
            // 1 concurrent call, messages are auto-completed, auto-renew duration
            new MessageHandlerOptions(1, true, Duration.ofMinutes(1)),
            executorService);

}
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этой статьи вы создали пространство имен служебной шины и другие ресурсы, необходимые для отправки и получения сообщений через очередь. Чтобы лучше изучить код, выполняющий отправку и получение сообщений, см. следующее руководство по служебной шине:

> [!div class="nextstepaction"]
> [Обновление данных о запасах с помощью Azure CLI и Java](./service-bus-tutorial-topics-subscriptions-cli.md)

[бесплатная учетная запись]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
