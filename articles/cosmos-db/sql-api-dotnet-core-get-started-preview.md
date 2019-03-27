---
title: Создание консольного приложения .NET Core для управления данными в учетной записи SQL API Azure Cosmos DB (предварительный выпуск пакета SDK версия 3)
description: В этом руководстве описано создание консольного приложения C# и оперативной базы данных с помощью пакета SDK для .NET Core для API SQL в службе Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: 828d0ced775d46b0e966969acb3220d2a678c57b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440052"
---
# <a name="build-a-net-core-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account-sdk-version-3-preview"></a>Создание консольного приложения .NET Core для управления данными в учетной записи SQL API Azure Cosmos DB (предварительный выпуск пакета SDK версия 3)

> [!div class="op_single_selector"]
>
> - [.NET Core (предварительная версия)](sql-api-dotnet-core-get-started-preview.md)
> - [.NET Core](sql-api-dotnetcore-get-started.md)
> - [.NET (предварительная версия)](sql-api-dotnet-get-started-preview.md)
> - [.NET](sql-api-get-started.md)
> - [Java](sql-api-java-get-started.md)
> - [Async Java](sql-api-async-java-get-started.md)
> - [Node.js](sql-api-nodejs-get-started.md)

Добро пожаловать в руководство по началу работы с .NET Core в API SQL для Azure Cosmos DB. После изучения этого руководства у вас будет консольное приложение .NET Core, которое создает ресурсы Azure Cosmos DB и отправляет запросы к ним. Для работы с этим руководством используется [версия 3.0 и более поздние](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) пакета SDK Azure Cosmos DB .NET, предназначенного для [.NET Standard 2.0.](https://docs.microsoft.com/dotnet/standard/net-standard)

Темы, рассматриваемые в этом руководстве:

> [!div class="checklist"]
>
> - создание учетной записи Azure Cosmos и подключение к ней;
> - настройка проекта в Visual Studio;
> - создание базы данных и контейнера;
> - добавление элементов в контейнер;
> - выполнение запросов контейнера;
> - выполнение операций CRUD с элементом;
> - удаление базы данных.

У вас нет времени на создание приложений? Не беспокойтесь! Полное решение доступно на [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started). Краткие инструкции см. в разделе [Получение полного решения NoSQL для этого руководства](#GetSolution).

Хотите создать приложение Xamarin iOS, Android или Forms с помощью API SQL и пакета SDK для .NET Core? См. [Создание мобильных приложений с помощью Xamarin и Azure Cosmos DB](mobile-apps-with-xamarin.md).

## <a name="prerequisites"></a>Предварительные требования

- Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

- Если вы еще не установили Visual Studio 2017, вы можете скачать и использовать бесплатную среду [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Если вы разрабатываете приложения универсальной платформы Windows (UWP), используйте среду **Visual Studio 2017 версии 15.4** или более поздней версии. Убедитесь, что при установке Visual Studio доступна рабочая нагрузка **разработки для Azure**.

  - Если вы работаете в MacOS или Linux, вы можете разрабатывать приложения .NET Core с помощью командной строки, установив для этого пакет [SDK для .NET Core](https://www.microsoft.com/net/core#macos) с учетом платформы.

  - Если вы работаете в Windows, приложения .NET Core можно разрабатывать с помощью командной строки, установив для этого пакет [SDK для .NET Core](https://www.microsoft.com/net/core#windows).

  - Можно использовать свой редактор или скачать бесплатный [Visual Studio Code](https://code.visualstudio.com/), который работает в Windows, Linux и MacOS.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Шаг 1. создание учетной записи Azure Cosmos DB;

Давайте создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к шагу [Настройка решения Visual Studio](#SetupVS). Если вы используете эмулятор Azure Cosmos DB, выполните действия, описанные в статье об [эмуляторе Azure Cosmos DB](local-emulator.md), чтобы его настроить и сразу перейти к [настройке проекта Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Шаг 2. Настройка проекта Visual Studio

1. Откройте **Visual Studio 2017** у себя на компьютере.
1. В меню **Файл** выберите пункт **Создать**, а затем — **Проект**.
1. В диалоговом окне **Новый проект** выберите **Visual C#** / **Консольное приложение (.NET Core)**, а затем укажите имя проекта и нажмите кнопку **ОК**.
   ![Снимок экрана с окном нового проекта](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-new-project.png)
1. В **обозревателе решений** щелкните правой кнопкой мыши новое консольное приложение (оно находится в решении Visual Studio). Затем щелкните **Управление пакетами NuGet...**

   ![Снимок экрана контекстного меню проекта](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-manage-nuget.png)

1. На вкладке **NuGet** щелкните **Обзор** и в поле поиска введите **Microsoft.Azure.Cosmos**.
1. В результатах найдите **Microsoft.Azure.Cosmos** и щелкните **Установить**.
   Идентификатором пакета для клиентской библиотеки API SQL Azure Cosmos DB является [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Снимок экрана меню NuGet для поиска пакета SDK клиента Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

   Если появится сообщение о просмотре изменений в решении, нажмите кнопку **ОК**. Если появится сообщение о принятии условий лицензионного соглашения, щелкните **Принимаю**.

Отлично! Теперь, когда мы завершили настройку, начнем писать код. Вы можете найти проект готового кода для этого руководства в [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started/).

## <a id="Connect"></a> Шаг 3. Подключение к учетной записи Azure Cosmos DB

1. Во-первых, замените ссылки в начале приложения C# в файле **Program.cs** на такие:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;
   ```

1. Теперь добавьте эти константы и переменные в открытый класс `Program`.

   ```csharp
   public class Program
   {
       // ADD THIS PART TO YOUR CODE

       // The Azure Cosmos DB endpoint for running this sample.
       private static readonly string EndpointUri = "<your endpoint here>";
       // The primary key for the Azure Cosmos account.
       private static readonly string PrimaryKey = "<your primary key>";

       // The Cosmos client instance
       private CosmosClient cosmosClient;

       // The database we will create
       private CosmosDatabase database;

       // The container we will create.
       private CosmosContainer container;

       // The name of the database and container we will create
       private string databaseId = "FamilyDatabase";
       private string containerId = "FamilyContainer";
   }
   ```

   Обратите внимание, что если вам знакома предыдущая версия пакета SDK для .NET, вы уже видели термины "коллекция" и "документ". Так как Azure Cosmos DB поддерживает несколько моделей API, версия 3.0+ пакета SDK для .NET использует общие термины "контейнер" и "элемент". Контейнер может представлять собой коллекцию, граф или таблицу. Элемент может представлять собой документ, ребро, вершину или запись и является содержимым внутри контейнера. [Подробнее о базах данных, контейнерах и элементах.](databases-containers-items.md)

1. Получите URL-адрес конечной точки и первичный ключ на [портале Azure](https://portal.azure.com).

   На портале Azure перейдите к учетной записи Azure Cosmos DB, а затем щелкните **Ключи**.

   Скопируйте универсальный код ресурса (URI) с портала и вставьте его в параметр `<your endpoint URL>` в файле `Program.cs`. Скопируйте на портале значение поля "Первичный ключ" и вставьте его в параметр `<your primary key>`.

   ![Снимок экрана получения ключей Azure Cosmos DB на портале Azure](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Далее мы создадим новый экземпляр `CosmosClient` и настроим формирование шаблонов для нашей программы.

   В методе **Main** добавьте новую асинхронную задачу с именем **GetStartedDemoAsync**, которая создает экземпляр `CosmosClient`. Мы будем использовать **GetStartedDemoAsync** в качестве точки входа, которая вызывает методы для работы с ресурсами Azure Cosmos DB.

   ```csharp
   public static async Task Main(string[] args)
   {
   }

   // ADD THIS PART TO YOUR CODE
   /*
       Entry point to call methods that operate on Azure Cosmos DB resources in this sample
   */
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
   }
   ```

1. Добавьте указанный далее код, чтобы запустить асинхронную задачу **GetStartedDemoAsync** из метода **Main**. Метод **Main** будет перехватывать исключения и записывать их в консоли.

   ```csharp
   public static async Task Main(string[] args)
   {
       // ADD THIS PART TO YOUR CODE
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();
       }
       catch (CosmosException de)
       {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
       }
       catch (Exception e)
       {
           Console.WriteLine("Error: {0}\n", e);
       }
       finally
       {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
       }
   }
   ```

1. Нажмите клавишу **F5**, чтобы запустить приложение. Результаты в окне консоли отображают сообщение `End of demo, press any key to exit.`. Это означает, что подключение к Azure Cosmos DB установлено. Теперь можно закрыть окно консоли.

Поздравляем! Вы успешно подключились к учетной записи Azure Cosmos DB.

## <a name="step-4-create-a-database"></a>Шаг 4. Создание базы данных

Вы можете создать базу данных с помощью метода [**CreateDatabaseIfNotExistsAsync**](https://aka.ms/CosmosDotnetAPIDocs) или функции [**CreateDatabaseAsync**](https://aka.ms/CosmosDotnetAPIDocs) класса **CosmosDatabases**. База данных представляет собой логический контейнер элементов, разделенных между контейнерами.

1. Скопируйте и вставьте метод **CreateDatabase** ниже метода **GetStartedDemoAsync**. Функция **CreateDatabase** создаст новую базу данных с идентификатором `FamilyDatabase`, указанным в поле `databaseId`, если она не была создана ранее.

   ```csharp
   /*
       Create the database if it does not exist
   */
   private async Task CreateDatabase()
   {
       // Create a new database
       this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
       Console.WriteLine("Created Database: {0}\n", this.database.Id);
   }
   ```

1. Скопируйте и вставьте приведенный ниже код, где вы создали экземпляр CosmosClient, для вызова добавленного метода **CreateDatabase**.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

       //ADD THIS PART TO YOUR CODE
       await this.CreateDatabase();
   }
   ```

   На этом этапе ваш код должен выглядеть следующим образом с заполненными значениями конечной точки и первичного ключа. Обратите внимание, что ваше пространство имен будет отличаться в зависимости от названия проекта.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;

   namespace CosmosGettingStartedDotnetCoreTutorial
   {
       class Program
       {
           // The Azure Cosmos DB endpoint for running this sample.
           private static readonly string EndpointUri = "<your endpoint here>";
           // The primary key for the Azure Cosmos account.
           private static readonly string PrimaryKey = "<your primary key>";

           // The Cosmos client instance
           private CosmosClient cosmosClient;

           // The database we will create
           private CosmosDatabase database;

           // The container we will create.
           private CosmosContainer container;

           // The name of the database and container we will create
           private string databaseId = "FamilyDatabase";
           private string containerId = "FamilyContainer";

           public static async Task Main(string[] args)
           {
               try
               {
                   Console.WriteLine("Beginning operations...");
                   Program p = new Program();
                   await p.GetStartedDemoAsync();
               }
               catch (CosmosException de)
               {
                   Exception baseException = de.GetBaseException();
                   Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
               }
               catch (Exception e)
               {
                   Console.WriteLine("Error: {0}\n", e);
               }
               finally
               {
                   Console.WriteLine("End of demo, press any key to exit.");
                   Console.ReadKey();
               }
           }

           /*
               Entry point to call methods that operate on Azure Cosmos DB resources in this sample
           */
           public async Task GetStartedDemoAsync()
           {
               // Create a new instance of the Cosmos Client
               this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
               await this.CreateDatabase();
           }

           /*
               Create the database if it does not exist
           */
           private async Task CreateDatabase()
           {
               // Create a new database
               this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
               Console.WriteLine("Created Database: {0}\n", this.database.Id);
           }
       }
   }
   ```

Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно создали базу данных Azure Cosmos DB.

## <a id="CreateColl"></a>Шаг 5. Создание контейнера

> [!WARNING]
> При вызове метода **CreateContainerIfNotExistsAsync** будет создан контейнер с ценовыми требованиями. Дополнительные сведения см. на нашей [странице цен](https://azure.microsoft.com/pricing/details/cosmos-db/).

Контейнер можно создать с помощью метода [**CreateContainerIfNotExistsAsync**](https://aka.ms/CosmosDotnetAPIDocs) или функции [**CreateContainerAsync**](https://aka.ms/CosmosDotnetAPIDocs) из класса **CosmosContainers**. Контейнер состоит из элементов (которые в случае API SQL являются документами JSON) и связанной логики приложения JavaScript на стороне сервера, например хранимые процедуры, определяемые пользователем функции и триггеры.

1. Скопируйте и вставьте метод **CreateContainer** ниже метода **CreateDatabase**. Функция **CreateContainer** создаст новый контейнер с идентификатором `FamilyContainer`, указанным в поле `containerId`, если она не была создана ранее.

   ```csharp
   /*
       Create the container if it does not exist.
       Specify "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
   */
   private async Task CreateContainer()
   {
       // Create a new container
       this.container = await this.database.Containers.CreateContainerIfNotExistsAsync(containerId, "/LastName");
       Console.WriteLine("Created Container: {0}\n", this.container.Id);
   }
   ```

1. Скопируйте и вставьте приведенный ниже код, где вы создали экземпляр CosmosClient, для вызова добавленного метода **CreateContainer**.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();

       //ADD THIS PART TO YOUR CODE
       await this.CreateContainer();
   }
   ```

Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно создали контейнер Azure Cosmos DB.

## <a id="CreateDoc"></a>Шаг 6. Добавление элементов в контейнер

Элемент можно создать с помощью функции [**CreateItemAsync**](https://aka.ms/CosmosDotnetAPIDocs) класса **CosmosItems**. При использовании API SQL элементы проецируются как документы, которые являются содержимым JSON, определенным пользователем (произвольным). Теперь можно вставить элемент в ваш контейнер Azure Cosmos DB.

Сначала необходимо создать класс **Family**, который будет представлять объекты, хранящиеся в Azure Cosmos DB в этом примере. Мы также создадим подклассы **Parent**, **Child**, **Pet** и **Address**, используемые в классе **Family**. Обратите внимание, документы должны иметь свойство **Id**, сериализуемое как **id** в файле JSON.

1.  Выберите **Ctrl + Shift + A**, чтобы открыть диалоговое окно **Добавить новый элемент**. Добавьте в ваш проект новый класс **Family.cs**.

    ![Снимок экрана добавления в проект нового класса Family.cs](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1.  Скопируйте и вставьте классы **Family**, **Parent**, **Child**, **Pet** и **Address** в **Family.cs**. Обратите внимание, что ваше пространство имен будет отличаться в зависимости от названия проекта.

    ```csharp
    using Newtonsoft.Json;

    namespace CosmosGettingStartedDotnetCoreTutorial
    {
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
    }
    ```

1.  Перейдите к файлу **Program.cs** и добавьте метод **AddItemsToContainer** в свой метод **CreateContainer**.
    Перед созданием элемента код проверяет, нет ли элемента с таким же идентификатором. Мы вставим два элемента: по одному для семьи Андерсен и семьи Вейкфилд.

    ```csharp
     /*
         Add Family items to the container
     */
     private async Task AddItemsToContainer()
     {
         // Create a family object for the Andersen family
         Family andersenFamily = new Family
         {
             Id = "Andersen.1",
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

         // Read the item to see if it exists. Note ReadItemAsync will not throw an exception if an item does not exist. Instead, we check the StatusCode property off the response object.
         CosmosItemResponse<Family> andersenFamilyResponse = await this.container.Items.ReadItemAsync<Family>(andersenFamily.LastName, andersenFamily.Id);

         if (andersenFamilyResponse.StatusCode == HttpStatusCode.NotFound)
         {
             // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
             andersenFamilyResponse = await this.container.Items.CreateItemAsync<Family>(andersenFamily.LastName, andersenFamily);

             // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
             //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
             Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
         }
         else
         {
             Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
         }

         // Create a family object for the Wakefield family
         Family wakefieldFamily = new Family
         {
             Id = "Wakefield.7",
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

         // Read the item to see if it exists
         CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily.Id);

         if (wakefieldFamilyResponse.StatusCode == HttpStatusCode.NotFound)
         {
             // Create an item in the container representing the Wakefield family. Note we provide the value of the partition key for this item, which is "Wakefield"
             wakefieldFamilyResponse = await this.container.Items.CreateItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily);

             // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
             //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
             Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", wakefieldFamilyResponse.Resource.Id, wakefieldFamilyResponse.RequestCharge);
         }
         else
         {
             Console.WriteLine("Item in database with id: {0} already exists\n", wakefieldFamilyResponse.Resource.Id);
         }
     }
    
    ```

1.  Добавьте вызов в `AddItemsToContainer` в метод `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase();
        await this.CreateContainer();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainer();
    }
    ```

Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно создали два элемента Azure Cosmos DB.

## <a id="Query"></a>Шаг 7. Запрашивание ресурсов Azure Cosmos DB

Azure Cosmos DB поддерживает [полнофункциональные запросы](sql-api-sql-query.md) к документам JSON, хранящимся в каждой коллекции. В следующем примере кода показано, как выполнить запрос к элементам, вставленным на предыдущем шаге.

1. Скопируйте и вставьте метод **RunQuery** ниже метода **AddItemsToContainer**.

   ```csharp
   /*
       Run a query (using Azure Cosmos DB SQL syntax) against the container
   */
   private async Task RunQuery()
   {
       var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";
       var partitionKeyValue = "Andersen";

       Console.WriteLine("Running query: {0}\n", sqlQueryText);

       CosmosSqlQueryDefinition queryDefinition = new CosmosSqlQueryDefinition(sqlQueryText);
       CosmosResultSetIterator<Family> queryResultSetIterator = this.container.Items.CreateItemQuery<Family>(queryDefinition, partitionKeyValue);

       List<Family> families = new List<Family>();

       while (queryResultSetIterator.HasMoreResults)
       {
           CosmosQueryResponse<Family> currentResultSet = await queryResultSetIterator.FetchNextSetAsync();
           foreach (Family family in currentResultSet)
           {
               families.Add(family);
               Console.WriteLine("\tRead {0}\n", family);
           }
       }
   }
   ```

1. Добавьте вызов в `RunQuery` в метод `GetStartedDemoAsync`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();

       //ADD THIS PART TO YOUR CODE
       await this.RunQuery();
   }
   ```

Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно выполнили запрос контейнера Azure Cosmos DB.

## <a id="ReplaceItem"></a>Шаг 8. Замена элемента JSON

Теперь мы будем обновлять элемент в Azure Cosmos DB.

1. Скопируйте и вставьте метод **ReplaceFamilyItem** ниже метода **RunQuery**. Обратите внимание, что мы меняем свойство `IsRegistered` семейства и параметр `Grade` одного из дочерних элементов.

   ```csharp
   /*
   Update an item in the container
   */
   private async Task ReplaceFamilyItem()
   {
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>("Wakefield", "Wakefield.7");
       var itemBody = wakefieldFamilyResponse.Resource;

       // update registration status from false to true
       itemBody.IsRegistered = true;
       // update grade of child
       itemBody.Children[0].Grade = 6;

       // replace the item with the updated content
       wakefieldFamilyResponse = await this.container.Items.ReplaceItemAsync<Family>(itemBody.LastName, itemBody.Id, itemBody);
       Console.WriteLine("Updated Family [{0},{1}]\n. Body is now: {2}\n", itemBody.LastName, itemBody.Id, wakefieldFamilyResponse.Resource);
   }
   ```

1. Добавьте вызов в `ReplaceFamilyItem` в метод `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();

       //ADD THIS PART TO YOUR CODE
       await this.ReplaceFamilyItem();
   }
   ```

Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно заменили элемент Azure Cosmos DB.

## <a id="DeleteDocument"></a>Шаг 9. Удаление элемента

Теперь мы будем удалять элемент в Azure Cosmos DB.

1. Скопируйте и вставьте метод **DeleteFamilyItem** под **ReplaceFamilyItem**.

   ```csharp
   /*
   Delete an item in the container
   */
   private async Task DeleteFamilyItem()
   {
       var partitionKeyValue = "Wakefield";
       var familyId = "Wakefield.7";

       // Delete an item. Note we must provide the partition key value and id of the item to delete
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.DeleteItemAsync<Family>(partitionKeyValue, familyId);
       Console.WriteLine("Deleted Family [{0},{1}]\n", partitionKeyValue, familyId);
   }
   ```

1. Добавьте вызов в `DeleteFamilyItem` в метод `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteFamilyItem();
   }
   ```

Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно удалили элемент Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Шаг 10. Удаление базы данных

Теперь удалим базу данных. Удаление созданной базы данных приведет к удалению базы данных и всех дочерних ресурсов (контейнеров, элементов и любых хранимых процедур, определяемых пользователем функций и триггеров). Мы также удалим экземпляр **CosmosClient**.

1. Скопируйте и вставьте метод **DeleteDatabaseAndCleanup** ниже метода **DeleteFamilyItem**.

   ```csharp
   /*
   Delete the database and dispose of the Cosmos Client instance
   */
   private async Task DeleteDatabaseAndCleanup()
   {
       CosmosDatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
       // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

       Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

       //Dispose of CosmosClient
       this.cosmosClient.Dispose();
   }
   ```

1. Добавьте вызов в `DeleteDatabaseAndCleanup` в метод `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();
       await this.DeleteFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteDatabaseAndCleanup();
   }
   ```

Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно удалили базу данных Azure Cosmos DB.

## <a id="Run"></a>Шаг 11. Запуск консольного приложения C#

Чтобы создать приложение в режиме отладки, откройте Visual Studio и нажмите клавишу **F5**.

В окне консоли должны отобразиться все выходные данные вашего приложения. Они должны содержать результаты обработки добавленных запросов. При этом выглядеть они должны примерно так, как показано в примере ниже.

```
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Поздравляем! Вы завершили работу с этим руководством, и теперь у вас есть работающее консольное приложение C#.

## <a id="GetSolution"></a>Получение готового решения для этого руководства

Чтобы собрать решение GetStarted, которое содержит все примеры из данной статьи, вам понадобится следующее:

- Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/).
- [Учетная запись Azure Cosmos DB][cosmos-db-create-account]
- решение [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started) , доступное в GitHub.

Чтобы в Visual Studio восстановить ссылки на API SQL для пакета SDK .NET Core для Azure Cosmos DB, в обозревателе решений щелкните правой кнопкой мыши решение **GetStarted**, а затем выберите пункт **Restore NuGet Package** (Восстановление пакета NuGet). Затем в файле **Program.cs** обновите значения `EndpointUri` и `PrimaryKey` согласно инструкциям в разделе о [подключении к учетной записи Azure Cosmos DB](#Connect).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как создать приложение .NET Core для управления данными API SQL для Azure Cosmos DB. Теперь можно импортировать дополнительные данные в учетную запись Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Импорт данных в DocumentDB с помощью средства миграции базы данных](import-data.md)

[cosmos-db-create-account]: create-sql-api-dotnet-preview.md#create-account
