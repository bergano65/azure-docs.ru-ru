---
title: Создание базы данных документов Azure Cosmos DB с помощью Java
description: В этой статье представлен пример кода Java, который можно использовать для подключения и выполнения запросов к API SQL в Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/24/2018
ms.author: sngun
ms.openlocfilehash: 0e80fbc96bf1241308c80d108e9f9580122b801b
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587724"
---
# <a name="quickstart-build-a-java-application-using-azure-cosmos-db-sql-api-account"></a>Краткое руководство. Создание веб-приложения Java с использованием учетной записи API SQL для Azure Cosmos DB


> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (предварительная версия)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

В этом кратком руководстве вы узнаете, как создавать ресурсы учетной записи [API SQL](sql-api-introduction.md) для Azure Cosmos DB и управлять bvb с помощью приложения Java. Сначала создайте учетную запись API SQL для Azure Cosmos DB с помощью портала Azure, затем создайте приложение Java с использованием [пакета SDK Java для SQL](sql-api-sdk-async-java.md) и добавьте ресурсы в учетную запись Cosmos DB с помощью приложения Java. Указания в этом руководстве применимы к любой операционной системе, с которой может работать Java. Выполнив инструкции в этом руководстве, вы узнаете, как создавать и изменять базы данных и коллекции Cosmos DB с помощью пользовательского интерфейса или программных средств (по вашему выбору).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Кроме того, сделайте следующее: 

* [Комплект разработчика Java (JDK) 1.8+](https://aka.ms/azure-jdks).
    * В Ubuntu выполните команду `apt-get install default-jdk`, чтобы установить JDK.
    * Обязательно настройте переменную среды JAVA_HOME так, чтобы она указывала на папку, в которой установлен пакет JDK.
* [Скачайте](https://maven.apache.org/download.cgi) и [установите](https://maven.apache.org/install.html) двоичный архив [Maven](https://maven.apache.org/).
    * В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.
* [Git](https://www.git-scm.com/)
    * В Ubuntu выполните команду `sudo apt-get install git`, чтобы установить Git.

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Перед созданием базы данных документов необходимо создать в Azure Cosmos DB учетную запись API SQL.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Добавление коллекции

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Добавление демонстрационных данных

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Обращение к данным

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь перейдем к работе с кодом. Давайте клонируем приложение API SQL из GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started
    ```

## <a name="review-the-code"></a>Просмотр кода

Этот шаг не является обязательным. Если вы хотите узнать, как создать в коде ресурсы базы данных, изучите приведенные ниже фрагменты кода. Вы можете сразу перейти к [запуску приложения](#run-the-app). 

* Инициализация `AsyncDocumentClient`. [AsyncDocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) является логическим представлением службы баз данных Azure Cosmos DB на стороне клиента. Этот клиент позволяет настраивать и выполнять запросы к службе.

    ```java
    client = new AsyncDocumentClient.Builder()
             .withServiceEndpoint(YOUR_COSMOS_DB_ENDPOINT)
             .withMasterKeyOrResourceToken(YOUR_COSMOS_DB_MASTER_KEY)
             .withConnectionPolicy(ConnectionPolicy.GetDefault())
             .withConsistencyLevel(ConsistencyLevel.Eventual)
             .build();
    ```

* Создание [Database](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.database).

    ```java
    Database databaseDefinition = new Database();
    databaseDefinition.setId(databaseName);
    
    client.createDatabase(databaseDefinition, null)
            .toCompletable()
            .await();
    ```

* Создание [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection).

    ```java
    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.setId(collectionName);

    //...

    client.createCollection(databaseLink, collectionDefinition, requestOptions)
            .toCompletable()
            .await();
    ```

* Создание документа с помощью метода [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document).

    ```java
    // Any Java object within your code
    // can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    client.createDocument(collectionLink, family, null, true)
            .toCompletable()
            .await();

    ```

* SQL-запросы к JSON можно выполнить с помощью метода [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments?view=azure-java-stable).

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);
    queryOptions.setMaxDegreeOfParallelism(-1);

    String collectionLink = String.format("/dbs/%s/colls/%s",
            databaseName,
            collectionName);
    Iterator<FeedResponse<Document>> it = client.queryDocuments(
            collectionLink,
            "SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
            queryOptions).toBlocking().getIterator();

    System.out.println("Running SQL query...");
    while (it.hasNext()) {
        FeedResponse<Document> page = it.next();
        System.out.println(
                String.format("\tRead a page of results with %d items",
                        page.getResults().size()));
        for (Document doc : page.getResults()) {
            System.out.println(String.format("\t doc %s", doc));
        }
    }
    ```    

## <a name="run-the-app"></a>Запуск приложения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения, и запустите приложение, используя данные конечной точки. Так вы обеспечите обмен данными между приложением и размещенной базой данных.


1. В окне терминала Git выполните команду `cd`, чтобы перейти к папке с примером кода.

    ```bash
    cd azure-cosmos-db-sql-api-async-java-getting-started/azure-cosmosdb-get-started
    ```

2. В окне терминала Git введите приведенную ниже команду, чтобы установить необходимые пакеты Java.

    ```bash
    mvn package
    ```

3. В окне терминала Git используйте следующую команду, чтобы запустить приложение Java (замените YOUR_COSMOS_DB_HOSTNAME заключенным в кавычки значением URI с портала, а YOUR_COSMOS_DB_MASTER_KEY — заключенным в кавычки первичным ключом с портала).

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    В окне терминала отобразится уведомление о создании базы данных FamilyDB. 
    
4. Нажмите клавишу, чтобы создать базу данных, а затем нажмите еще одну клавишу, чтобы создать коллекцию. 

    Вернитесь в обозреватель данных в браузере, чтобы увидеть, содержатся ли в нем база данных FamilyDB и коллекция FamilyCollection.

5. Перейдите в окно консоли и нажмите клавишу, чтобы создать первый документ, а затем нажмите еще одну клавишу, чтобы создать второй документ. После этого вернитесь в обозреватель данных для их просмотра. 

6. Нажмите клавишу, чтобы выполнить запрос и просмотреть выходные данные в окне консоли. 

7. Это приложение не позволяет удалять созданные ресурсы. Вернитесь на портал и [удалите там ресурсы](#clean-up-resources)  из учетной записи, чтобы за них не взималась плата.

    ![Вывод на консоль](./media/create-sql-api-java/rxjava-console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как создать учетную запись Azure Cosmos DB, базу данных документов и коллекцию с помощью обозревателя данных, а также как запустить приложение, чтобы эти операции выполнялись программой. Теперь вы можете импортировать дополнительные данные в коллекцию Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Импорт данных в DocumentDB с помощью средства миграции базы данных](import-data.md)
