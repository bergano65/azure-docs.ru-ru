---
title: Руководство. Создание приложения Java с помощью пакета SDK Async Java для управления учетной записью API SQL в Azure Cosmos DB
description: В этом руководстве показано, как с помощью приложения Async Java хранить данные в учетной записи API SQL в Azure Cosmos DB и получить к ним доступ.
keywords: руководство nosql, оперативная база данных, консольное приложение java
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 89593db11580a0892022f2ee0e11160c2e912a2e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083943"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Руководство. Создание приложения Java с помощью пакета SDK Async Java для управления данными, хранящимися в учетной записи API SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (предварительная версия)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (предварительная версия)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Как у разработчика у вас могут быть приложения, использующие данные документов NoSQL. Вы можете использовать учетную запись API SQL в Azure Cosmos DB для хранения этих данных документов и получения к ним доступа. В этом руководстве показано, как создать приложение Java с помощью пакета SDK Async Java для хранения данных документов и управления ими. 

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * создание учетной записи Azure Cosmos и подключение к ней;
> * настройка решения;
> * создание коллекции;
> * создание документов JSON;
> * выполнение запросов к коллекции;

## <a name="prerequisites"></a>Предварительные требования

Убедитесь, что у вас есть указанные ниже ресурсы.

* Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads).

* [Комплект разработчика Java (JDK) 8+](https://aka.ms/azure-jdks).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

Создайте учетную запись Azure Cosmos, выполнив следующие действия:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Клонирование репозитория GitHub

[Чтобы приступить к работе с Azure Cosmos DB и Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started), сначала клонируйте репозиторий GitHub. Например, чтобы получить образец проекта в локальной среде, в локальном каталоге выполните следующую команду.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

Каталог содержит файл `pom.xml` и папку `src/main/java/com/microsoft/azure/cosmosdb/sample`, содержащую исходный код Java, включая `Main.java`. Проект содержит код, необходимый для выполнения операций с Azure Cosmos DB, таких как создание документов и запрос данных в коллекции. В файле `pom.xml` содержится зависимость от [пакета Java SDK для Azure Cosmos DB в Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Подключение к учетной записи Azure Cosmos

Далее вернитесь на [портал Azure](https://portal.azure.com), чтобы получить конечную точку и первичный главный ключ. Конечная точка и первичный ключ Azure Cosmos DB позволяют приложению предоставлять данные о расположении, в котором будет устанавливаться подключение, делая подключение вашего приложения доверенным для Azure Cosmos DB. Файл `AccountSettings.java` содержит первичный ключ и значения универсального кода ресурса (URI). 

На портале Azure перейдите в учетную запись Azure Cosmos, а затем щелкните **Ключи**. Скопируйте на портале значения полей URI и "Первичный ключ" и вставьте их в файл `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Снимок экрана получения ключей на портале][keys]

## <a name="initialize-the-client-object"></a>Инициализация объекта клиента

Инициализируйте объект клиента с помощью значений узла универсального кода ресурса (URI) и первичного ключа, определенных в файле AccountSettings.java.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Создание базы данных

Создайте базу данных Azure Cosmos DB с использованием метода `createDatabaseIfNotExists()` в классе DocumentClient. База данных представляет собой логический контейнер для хранения документов JSON, разделенных между коллекциями.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>Создание коллекции

Вы можете создать коллекцию с использованием метода `createDocumentCollectionIfNotExists()` в классе DocumentClient. Коллекция представляет собой контейнер документов JSON и связанной логики приложения на JavaScript.

> [!WARNING]
> Элемент **createCollection** создает коллекцию с зарезервированной пропускной способностью и соответствующей ценой. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>Создание документов JSON

Создайте документ с помощью метода createDocument класса DocumentClient. Документы относятся к пользовательскому (произвольному) содержимому JSON. Теперь мы можем добавить один или несколько документов. Файл src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java определяет семейство документов JSON. 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>Запрос ресурсов Azure Cosmos DB

Azure Cosmos DB поддерживает полнофункциональные запросы к документам JSON, хранящимся в каждой коллекции. Ниже приведен пример кода, который позволяет запросить документы в Azure Cosmos DB, используя синтаксис SQL с методом `queryDocuments`.

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>Запуск консольного приложения Java

Чтобы запустить приложение из консоли, перейдите в папку проекта и выполните компиляцию с помощью Maven:

```bash
mvn package
```

Команда `mvn package` позволяет скачать последнюю версию библиотеки Azure Cosmos DB из Maven и возвращает `GetStarted-0.0.1-SNAPSHOT.jar`. Затем запустите приложение, выполнив следующую команду:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Вы завершили работу с руководством по NoSQL и создали работающее консольное приложение Java.

## <a name="clean-up-resources"></a>Очистка ресурсов

Можно удалить группу ресурсов, учетную запись Azure Cosmos и все связанные ресурсы, когда они больше не нужны. Для этого выберите группу ресурсов для виртуальной машины, выберите **Удалить** и подтвердите имя удаляемой группы ресурсов.


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как создать приложение Java с помощью пакета SDK Async Java, чтобы управлять данными API SQL в Azure Cosmos DB. Теперь вы можете перейти к следующей статье:

> [!div class="nextstepaction"]
> [Руководство. Создание консольного приложения Node.js с помощью пакета SDK JavaScript для управления данными API SQL для Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
