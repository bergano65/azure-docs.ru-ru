---
title: Создание консольного приложения .NET для управления данными в учетной записи API SQL для Azure Cosmos DB
description: В этом руководстве описывается создание оперативной базы данных и консольного приложения C# с помощью API SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: 60c7e6b32f60d6f42d706489c41dbeea4af0d15d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342124"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Создание консольного приложения .NET для управления данными в учетной записи API SQL для Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (предварительная версия)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (предварительная версия)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Добро пожаловать в руководство по началу работы с API SQL для Azure Cosmos DB. После изучения этого руководства у вас будет консольное приложение, которое создает ресурсы Azure Cosmos DB и отправляет запросы к ним.

В этом учебнике описаны следующие процедуры.

> [!div class="checklist"]
>
> - Создание учетной записи Azure Cosmos DB и подключение к ней.
> - Настройка решения Visual Studio.
> - Создание базы данных
> - Создание коллекции
> - создание документов JSON;
> - Отправка запроса к коллекции.
> - Обновление документа JSON.
> - Удаление документа
> - Удаление базы данных

## <a name="prerequisites"></a>Предварительные требования

Visual Studio 2017 с установленной рабочей нагрузкой разработки для Azure.
- Вы можете скачать и использовать **бесплатную** версию [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**. 

Подписка Azure или бесплатная пробная учетная запись Cosmos DB.
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Если используете эмулятор Azure Cosmos DB, настройте его, следуя указаниям в разделе [Использование эмулятора Azure Cosmos для разработки и тестирования в локальной среде](local-emulator.md). Затем приступите к изучению руководства с раздела [Настройка решения Visual Studio](#SetupVS).
  
## <a name="get-the-completed-solution"></a>Получение готового решения

Если у вас нет времени на выполнение этого руководства или вам нужны только примеры кода, вы можете скачать готовое решение с портала [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

Чтобы запустить скачанное готовое решение, сделайте следующее. 

1. Убедитесь, что установлены все [необходимые компоненты](#prerequisites). 
1. Откройте скачанный файл решения *GetStarted.sln* в Visual Studio.
1. В **обозревателе решений** щелкните проект **GetStarted** правой кнопкой мыши и выберите **Управление пакетами NuGet**.
1. На вкладке **NuGet** выберите **Восстановить**, чтобы восстановить ссылки на пакет SDK .NET для Azure Cosmos DB.
1. Затем в файле *App.config* обновите значения `EndpointUrl` и `PrimaryKey` согласно инструкциям в разделе [Подключение к учетной записи Azure Cosmos DB](#Connect).
1. Выберите **Отладка** > **Запуск без отладки** или нажмите клавиши **Ctrl**+**F5**, чтобы выполнить сборку и запуск приложения.

## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

Следуйте приведенным ниже инструкциям, чтобы создать учетную запись Azure Cosmos DB на портале Azure. Если у вас уже есть учетная запись Azure Cosmos DB, которую вы хотите использовать, можно перейти к разделу [Настройка решения Visual Studio](#SetupVS). 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Настройка решения Visual Studio

1. В Visual Studio 2017 выберите **Файл** > **Создать** > **Проект**.
   
1. В диалоговом окне **Новый проект** выберите **Visual C#**  > **Консольное приложение (.NET Framework)** , укажите имя проекта *AzureCosmosDBApp* и нажмите кнопку **ОК**.
   
   ![Снимок экрана с окном нового проекта](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. В **обозревателе решений** щелкните проект **AzureCosmosDBApp** правой кнопкой мыши и выберите **Управление пакетами NuGet**.
   
   ![Контекстное меню проекта](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. На вкладке **NuGet** щелкните **Обзор** и в поле поиска введите *azure documentdb*.
   
1. Найдите и выберите **Microsoft.Azure.DocumentDB**. Щелкните **Установить**, если этот пакет еще не установлен.
   
   Идентификатором пакета для клиентской библиотеки API SQL Azure Cosmos DB является [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).

   > [!NOTE]
   > Если вы используете .NET Core, см. [документацию по .NET Core](./sql-api-dotnetcore-get-started.md).

   ![Снимок экрана меню NuGet для поиска пакета SDK клиента Azure Cosmos DB](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   Если появится сообщение о предварительном просмотре изменений в решении, нажмите кнопку **ОК**. Если появится сообщение о принятии условий лицензионного соглашения, щелкните **Я принимаю**.

## <a id="Connect"></a>Подключение к учетной записи Azure Cosmos DB

Теперь давайте приступим к написанию кода. Готовый файл *Project.cs* для нашего руководства доступен на портале [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

1. В **обозревателе решений** выберите файл *Program.cs*. В редакторе кода добавьте в начало этого файла приведенные ниже ссылки.
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. Затем добавьте следующие две константы и переменную `client` в `public class Program`.
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. URL-адрес конечной точки и первичный ключ позволяют приложению подключаться к вашей учетной записи Azure Cosmos DB, и учетная запись Azure Cosmos DB будет разрешать это подключение. Скопируйте ключи с [портала Azure](https://portal.azure.com) и вставьте их в код. 

   
   1. На навигационной панели слева для учетной записи Azure Cosmos DB выберите элемент **Ключи**.
      
      ![Просмотр и копирование ключа доступа на портале Azure](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. В разделе **Ключи для чтения и записи** скопируйте значение **URI** с помощью кнопки копирования справа и вставьте его в `<your endpoint URL>` в файле *Program.cs*. Например: 
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. Скопируйте значение **PRIMARY KEY** и вставьте его в `<your primary key>` в файле *Program.cs*. Например: 
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. После метода `Main` добавьте новую асинхронную задачу `GetStartedDemo`, которая создает экземпляр `DocumentClient` с именем `client`.
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```

   Если вы используете прокси-объект для подключения к Azure Cosmos DB, вместо него необходимо использовать следующий блок кода, чтобы создать объект DocumentClient. В примере в этом документе не используется прокси-объект, поэтому приведенный ниже пример используется только для справки.

   ```csharp
   HttpClientHandler handler = new HttpClientHandler()
   {
     Proxy = proxyObject
     UseProxy = true,
   };

   //Pass handler to the constructor of DocumentClient.
   DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey, handler);
   
   ```
   
1. Добавьте в метод `Main` следующий код для запуска задания `GetStartedDemo`. Метод `Main` будет перехватывать исключения и записывать их в консоль.
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. Нажмите клавишу **F5**, чтобы запустить приложение. 
   
1. Когда в окне консоли появится сообщение **End of demo, press any key to exit** (Работа демоверсии завершена, нажмите любую клавишу, чтобы выйти), это означает, что подключение установлено успешно. Для закрытия окна консоли нажмите любую клавишу. 

Вы успешно подключились к учетной записи Azure Cosmos DB. Теперь приступим к работе с ресурсами Azure Cosmos DB.  

## <a name="create-a-database"></a>Создание базы данных

[База данных](databases-containers-items.md#azure-cosmos-databases) Azure Cosmos DB представляет собой логический контейнер для хранения документов JSON, разделенных на коллекции. Создать базу данных можно с помощью метода [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) класса `DocumentClient`. 

1. Прежде чем добавлять код для создания базы данных, добавьте вспомогательный метод для записи на консоль. Скопируйте следующий метод `WriteToConsoleAndPromptToContinue` и вставьте его после метода `GetStartedDemo` в коде.
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. Скопируйте следующую строку и вставьте ее в метод `GetStartedDemo` после строки `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);`. Этот код создает базу данных `FamilyDB`.
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. Нажмите клавишу **F5**, чтобы запустить приложение.

Вы успешно создали базу данных Azure Cosmos DB. Вы можете увидеть эту базу данных на [портале Azure](https://portal.azure.com), выбрав **обозреватель данных** в области навигации слева в учетной записи Azure Cosmos DB. 

## <a id="CreateColl"></a>Создание коллекции

Коллекция представляет собой контейнер документов JSON и связанную с ними логику в виде приложения JavaScript. Вы можете создать коллекцию с помощью метода [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) класса `DocumentClient`. 

> [!IMPORTANT]
> С помощью метода **CreateDocumentCollectionIfNotExistsAsync** можно создать новую коллекцию с зарезервированной пропускной способностью и соответствующей ценой. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 

1. Скопируйте следующий код и вставьте его в метод `GetStartedDemo` после строки `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });`. Этот код создает коллекцию документов `FamilyCollection`.
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. Нажмите клавишу **F5**, чтобы запустить приложение.

Вы успешно создали коллекцию документов Azure Cosmos DB. Вы можете просмотреть эту коллекцию в базе данных **FamilyDB** в **обозревателе данных** на портале Azure.  

## <a id="CreateDoc"></a>Создание документов JSON

Документы относятся к пользовательскому (произвольному) содержимому JSON. У документов должно быть свойство ID, сериализуемое как `id` в JSON-файле. Для создания документов используется метод [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) класса `DocumentClient`. 

> [!TIP]
> Если у вас уже есть данные, которые вы хотите хранить в базе данных, то вы можете использовать [средство миграции данных](import-data.md) Azure Cosmos DB, чтобы импортировать их.
>

Следующий код создает два документа и вставляет их в коллекцию базы данных. Сначала необходимо создать класс `Family` и подклассы `Parent`, `Child`, `Pet` и `Address` для использования в `Family`. Затем следует создать метод `CreateFamilyDocumentIfNotExists`, после чего нужно создать и вставить два документа. 

1. Скопируйте классы `Family`, `Parent`, `Child`, `Pet`, `Address` и вставьте их в после метода `WriteToConsoleAndPromptToContinue` в коде.
   
   ```csharp
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
   ```
   
1. Скопируйте следующий метод `CreateFamilyDocumentIfNotExists` и вставьте его после класса `Address`, который вы только что добавили.
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. Скопируйте следующий код и вставьте его в конец метода `GetStartedDemo`, после строки `await client.CreateDocumentCollectionIfNotExistsAsync`. Этот код создает и вставляет два документа, по одному для семей Andersen и Wakefield.
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents = new Parent[]
        {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
            new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
            new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
            new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
            new Child
            {
                FamilyName = "Merriam",
                FirstName = "Jesse",
                Gender = "female",
                Grade = 8,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Goofy" },
                    new Pet { GivenName = "Shadow" }
                }
            },
            new Child
            {
                FamilyName = "Miller",
                FirstName = "Lisa",
                Gender = "female",
                Grade = 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. Нажмите клавишу **F5**, чтобы запустить приложение.

Вы успешно создали два документа Azure Cosmos DB. Вы можете просмотреть эти документы в базе данных **FamilyDB** и коллекции **FamilyCollection** в **обозревателе данных** на портале Azure.   

![Схема, иллюстрирующая иерархические отношения между учетной записью, базой данных, коллекцией и документами](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Запрос ресурсов Azure Cosmos DB

Azure Cosmos DB поддерживает расширенные [запросы](how-to-sql-query.md) к документам JSON, хранящимся в коллекциях. В следующем примере кода используется синтаксис LINQ и SQL для Azure Cosmos DB для выполнения запроса к примерам документов.

1. Скопируйте следующий метод `ExecuteSimpleQuery` и вставьте его после метода `CreateFamilyDocumentIfNotExists` в коде.
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. Скопируйте следующий код и вставьте его в конец метода `GetStartedDemo`, после строки `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);`.
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Нажмите клавишу **F5**, чтобы запустить приложение.

Указанный выше запрос возвращает полный элемент для семейства Andersen. Вы успешно выполнили запрос к коллекции Azure Cosmos DB.

На приведенной ниже схеме показан вызов синтаксиса SQL-запроса Azure Cosmos DB к коллекции. Эта же логика применяется к запросам LINQ.

![На схеме представлен масштаб и значение запроса, используемого в руководстве по NoSQL при создании консольного приложения C#.](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

Ключевое слово [FROM](sql-query-from.md) использовать в SQL-запросе необязательно, так как запросы Azure Cosmos DB привязаны только к одной коллекции. Вы можете заменить `FROM Families f` на `FROM root r` или любое другое имя переменной на ваш выбор. Azure Cosmos DB будет считать, что `Families`, `root` или любая другая переменная указывает на текущую коллекцию.

## <a id="ReplaceDocument"></a>Обновление документа JSON

API SQL для Azure Cosmos DB поддерживает обновление и замену документов JSON.  

1. Скопируйте следующий метод `ReplaceFamilyDocument` и вставьте его после метода `ExecuteSimpleQuery` в коде.
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. Скопируйте следующий код и вставьте его в конец метода `GetStartedDemo`, после строки `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");`. Этот код обновляет данные в одном из документов, а затем выполняет запрос повторно, чтобы показать измененный документ.
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Нажмите клавишу **F5**, чтобы запустить приложение.

Выходные данные запроса показывают, что значение `Grade` для дочернего элемента семьи Andersen изменен с `5` на `6`. Вы успешно обновили и заменили документ Azure Cosmos DB. 

## <a id="DeleteDocument"></a>Удаление документа JSON

API SQL для Azure Cosmos DB поддерживает удаление документов JSON.  

1. Скопируйте следующий метод `DeleteFamilyDocument` и вставьте его после метода `ReplaceFamilyDocument`.
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. Скопируйте следующий код и вставьте его в конец метода `GetStartedDemo`, после строки второй `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");`.
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. Нажмите клавишу **F5**, чтобы запустить приложение.

Вы успешно удалили документ Azure Cosmos DB. 

## <a id="DeleteDatabase"></a>Удаление базы данных

Удалите базу данных, которую вы создали, чтобы удалить ее и вместе со всеми дочерними ресурсами, включая коллекцию и документы. 

1. Скопируйте следующий код и вставьте его в конец метода `GetStartedDemo`, после строки `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");`. 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. Нажмите клавишу **F5**, чтобы запустить приложение.

Вы успешно удалили базу данных Azure Cosmos DB. В **обозревателе данных** для вашей учетной записи Azure Cosmos DB можно видеть, что база данных FamilyDB удалена. 

## <a id="Run"></a>Запуск готового консольного приложения C#

Чтобы выполнить сборку и запуск готового консольного приложения C# в режиме отладки, в Visual Studio нажмите клавишу **F5**. В окне консоли должны отобразиться выходные данные, как показано ниже.

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

Поздравляем! Вы ознакомились с руководством и создали работающее консольное приложение C#, которое создает, обновляет, удаляет ресурсы Azure Cosmos DB и отправляет к ним запросы.  

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об Azure Cosmos DB см. в статье [Добро пожаловать в базу данных Azure Cosmos DB](introduction.md).
* Более сложное руководство по ASP.NET MVC: [Руководство по ASP.NET MVC. Разработка веб-приложения ASP.NET MVC с использованием Azure Cosmos DB с помощью предварительной версии пакета SDK для .NET](sql-api-dotnet-application.md).
* Инструкции по тестированию масштабирования и производительности с помощью Azure Cosmos DB см. в разделе [Проверка производительности и масштабирования с помощью Azure Cosmos DB](performance-testing.md).
* Чтобы узнать, как организовать мониторинг запросов, использования и хранилища Azure Cosmos DB, ознакомьтесь с разделом [Мониторинг метрик производительности и хранения в Azure Cosmos DB](monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Query Playground](https://www.documentdb.com/sql/demo).

