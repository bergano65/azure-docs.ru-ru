---
title: Создание комплексного примера приложения Java SDK версии 4 Azure Cosmos DB с помощью веб-канала изменений
description: В этом руководстве описывается простое приложение API SQL Java, которое вставляет документы в контейнер Azure Cosmos DB, сохраняя материализованный вид контейнера с помощью веб-канала изменений.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: 5e8656e891d250547174aa3deb27a94eebaa0ba3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125678"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Создание приложения Java, использующего Azure Cosmos DB API SQL и обработчика веб-канала изменений

> [!IMPORTANT]  
> Дополнительные сведения о Azure Cosmos DB пакете SDK для Java версии 4 см. Azure Cosmos DB в статьях заметки о выпуске пакета SDK для Java версии 4, [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB [Советы по повышению производительности](performance-tips-java-sdk-v4-sql.md)пакета java SDK v4 и Azure Cosmos DB [руководство по устранению неполадок](troubleshoot-java-sdk-v4-sql.md)пакета SDK для Java v4.
>

В этом руководстве рассматривается простое приложение Java, которое использует API Azure Cosmos DB SQL для вставки документов в контейнер Azure Cosmos DB, сохраняя материализованный вид контейнера с помощью канала изменений и обработчика веб-канала изменений. Приложение Java взаимодействует с Azure Cosmos DB API SQL с помощью Azure Cosmos DB пакета SDK для Java версии 4.

## <a name="prerequisites"></a>Предварительные требования

* Универсальный код ресурса (URI) и ключ для учетной записи Azure Cosmos DB.

* Maven

* Java 8

## <a name="background"></a>Историческая справка

Веб-канал изменений Azure Cosmos DB предоставляет интерфейс, основанный на событиях, для активации действий в ответ на вставку документа. Этот канал изменений можно использовать множеством различных способов. Например, в приложениях, которые доступны как для чтения, так и для записи, главным применением веб-канала изменений является создание **материализованных представлений** контейнера в режиме реального времени по мере приема документов. Контейнер в материализованном представлении будет содержать те же данные, но секционированные для эффективного чтения. Благодаря этому приложение будет эффективно выполнять операции чтения и записи.

Работа по управлению событиями веб-канала изменений в основном выполняется библиотекой обработчика веб-канала изменений, встроенной в пакет SDK. Эта библиотека обладает достаточной мощностью для распространения событий веб-канала изменений между несколькими сотрудниками, если это необходимо. Все, что нужно сделать, — это предоставить обратный вызов библиотеки веб-каналов изменений.

Этот простой пример демонстрирует использование библиотеки обработчика веб-канала изменений одним исполнителем, создающим и удаляя документы из материализованных представлений.

## <a name="setup"></a>Настройка

Если вы еще не сделали этого, клонируйте репозиторий примера приложения:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

Откройте терминал в каталоге репозитория. Выполните сборку приложения с помощью следующей команды:

```bash
mvn clean package
```

## <a name="walkthrough"></a>Пошаговое руководство

1. В первую очередь проверьте наличие учетной записи Azure Cosmos DB. Откройте **портал Azure** в браузере, перейдите к своей учетной записи Azure Cosmos DB и в левой области перейдите к **Обозреватель данных**.

    ![Учетная запись Azure Cosmos DB](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Запустите приложение в окне терминала с помощью следующей команды:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Нажмите клавишу ВВОД, когда на экране появится следующее сообщение.

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    Затем вернитесь к портал Azure обозреватель данных в браузере. Вы увидите, что добавлена база данных **GroceryStoreDatabase** с тремя пустыми контейнерами: 

    * **InventoryContainer** — запись инвентаризации для нашего примера продуктового магазина, секционированная по элементу ```id```, который является UUID.
    * **InventoryContainer-pktype** — материализованное представление записи инвентаризации, оптимизированное для запросов по элементу ```type```
    * **Инвенториконтаинер-Lease** — для веб-канала изменений всегда требуется контейнер аренды. Аренда следит за ходом выполнения приложения при чтении веб-канала изменений.


    ![Пустые контейнеры](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. В окне терминала появится следующий запрос:

    ```bash
    Press enter to start creating the materialized view...
    ```

    Нажмите клавишу ВВОД. Теперь следующий блок кода будет выполнять и инициализировать обработчик веб-канала изменений в другом потоке: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Асинхронный API пакета SDK для Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for change feed processor start
    ```

    ```"SampleHost_1"``` — это имя рабочей роли обработчика канала изменений. ```changeFeedProcessorInstance.start()``` фактически запускает обработчик канала изменений.

    Вернитесь к портал Azure обозреватель данных в браузере. В разделе контейнера **InventoryContainer-leases** щелкните **элементы**, чтобы просмотреть его содержимое. Вы увидите, что обработчик канала изменений заполнил контейнер аренд. Это означает, что обработчик назначил рабочей роли ```SampleHost_1``` аренду некоторых секций **InventoryContainer**.

    ![Аренда](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Снова нажмите клавишу ВВОД в окне терминала. При этом активируется вставка 10 документов в **InventoryContainer**. Каждая Вставка документа отображается в веб-канале изменений как JSON; Следующий код обратного вызова обрабатывает эти события путем зеркального отображения документов JSON в материализованный представлении:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Асинхронный API пакета SDK для Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

1. Подождите 5–10 секунд, пока код выполняется. Затем вернитесь к портал Azure обозреватель данных и перейдите к **элементу инвенториконтаинер > Items**. Там должно быть показано, что выполняется вставка элементов в контейнер инвентаризации. Обратите внимание на ключ секции (```id```).

    ![Контейнер канала](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Теперь в обозревателе данных перейдите в раздел **InventoryContainer-pktype > элементы**. Это материализованный вид — элементы в этом контейнере **инвенториконтаинер** зеркального отображения, так как они были вставлены программно с помощью веб-канала изменений. Обратите внимание на ключ секции (```type```). Таким образом, это материализованное представление оптимизировано для фильтрации запросов по ```type```, что будет неэффективным для контейнера **InventoryContainer**, так как он секционирован по ```id```.

    ![Материализованное представление](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Мы удалим документ из **InventoryContainer** и **InventoryContainer-pktype** с помощью одного вызова ```upsertItem()```. Сначала рассмотрим портал Azure обозреватель данных. Мы удалим документ, для которого ```/type == "plums"```. Он обведен красной рамкой.

    ![Материализованное представление](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Нажмите клавишу ВВОД еще раз, чтобы вызвать функцию ```deleteDocument()``` в примере кода. Эта функция, показанная ниже, выполняет для новой версии документа операцию upsert, устанавливая ```/ttl == 5```. Теперь срок жизни документа равен 5 секундам. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Асинхронный API пакета SDK для Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    Для веб-канала изменений ```feedPollDelay``` задано значение 100 мс, поэтому канал изменений реагирует на это обновление почти мгновенно и вызовы, ```updateInventoryTypeMaterializedView()``` показанные выше. При вызове этой функции для новой версии документа выполняется операция upsert, устанавливающая 5-секундный срок жизни документа в **InventoryContainer-pktype**.

    В результате приблизительно через 5 секунд срок жизни документа истечет и он будет удален из обоих контейнеров.

    Эта процедура необходима, так как канал изменений только выдает события при вставке или обновлении элемента, а не при удалении элемента.

1. Нажмите клавишу ВВОД еще раз, чтобы закрыть программу и очистить ее ресурсы.
