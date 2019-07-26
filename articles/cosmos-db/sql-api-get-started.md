---
title: Создание консольного приложения .NET для управления данными в учетной записи API SQL для Azure Cosmos DB
description: Из этой статьи вы узнаете, как создать ресурсы API SQL для Azure Cosmos DB с помощью консольного приложения C#.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: kirankk
ms.openlocfilehash: 36d172daed487372401691c7046215fb6c4a63ee
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384945"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Создание консольного приложения .NET для управления данными в учетной записи API SQL для Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Добро пожаловать в руководство по началу работы с API SQL для Azure Cosmos DB. После изучения этого руководства у вас будет консольное приложение, которое создает ресурсы Azure Cosmos DB и отправляет запросы к ним. Для работы с этим руководством используется [версия 3.0 или более поздняя](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) пакета SDK .NET для Azure Cosmos DB, предназначенного для [.NET Framework](https://dotnet.microsoft.com/download) или [.NET Core](https://dotnet.microsoft.com/download).

Темы, рассматриваемые в этом руководстве:

> [!div class="checklist"]
> * создание учетной записи Azure Cosmos и подключение к ней;
> * настройка проекта в Visual Studio;
> * создание базы данных и контейнера;
> * добавление элементов в контейнер;
> * выполнение запросов контейнера;
> * выполнение операций CRUD с элементом;
> * удаление базы данных.

У вас нет времени? Не беспокойтесь! Полное решение доступно на [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Краткие инструкции см. в разделе [Получение полного решения для этого руководства](#GetSolution).

А теперь приступим к работе!

## <a name="prerequisites"></a>Предварительные требования

* Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Шаг 1. создание учетной записи Azure Cosmos DB;
Давайте создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к шагу [Настройка решения Visual Studio](#SetupVS). Если вы используете эмулятор Azure Cosmos DB, выполните действия, описанные в статье об [эмуляторе Azure Cosmos DB](local-emulator.md), чтобы его настроить и сразу перейти к [настройке проекта Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>Шаг 2. Настройка проекта Visual Studio
1. Откройте **Visual Studio 2017** у себя на компьютере.
1. В меню **Файл** выберите пункт **Создать**, а затем — **Проект**.
1. В диалоговом окне **Новый проект** выберите **Visual C#**  / **Консольное приложение (.NET Framework)** , а затем укажите имя проекта и нажмите кнопку **ОК**.
    ![Снимок экрана с окном нового проекта](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > Для целевой системы .NET Core в диалоговом окне **Новый проект** выберите **Visual C#**  / **Консольное приложение (.NET Core)** , а затем укажите имя проекта и нажмите кнопку **ОК**.

1. В **обозревателе решений** щелкните правой кнопкой мыши новое консольное приложение (оно находится в решении Visual Studio). Затем щелкните **Управление пакетами NuGet...**

    ![Снимок экрана контекстного меню проекта](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. На вкладке **NuGet** щелкните **Обзор** и в поле поиска введите **Microsoft.Azure.Cosmos**.
1. В результатах найдите **Microsoft.Azure.Cosmos** и щелкните **Установить**.
   Идентификатором пакета для клиентской библиотеки API SQL Azure Cosmos DB является [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Снимок экрана меню NuGet для поиска пакета SDK клиента Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Если появится сообщение о просмотре изменений в решении, нажмите кнопку **ОК**. Если появится сообщение о принятии условий лицензионного соглашения, щелкните **Принимаю**.

Отлично! Теперь, когда мы завершили настройку, начнем писать код. Вы можете найти проект готового кода для этого руководства в [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a> Шаг 3. Подключение к учетной записи Azure Cosmos DB
1. Во-первых, замените ссылки в начале приложения C# в файле **Program.cs** на такие:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Теперь добавьте эти константы и переменные в открытый класс ``Program``.

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
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

    Обратите внимание, что если вам знакома предыдущая версия пакета SDK для .NET, вы уже видели термины "коллекция" и "документ". Так как Azure Cosmos DB поддерживает несколько моделей API, версия 3.0+ пакета SDK для .NET использует общие термины "контейнер" и "элемент". Контейнер может представлять собой коллекцию, граф или таблицу. Элемент может представлять собой документ, ребро, вершину или запись и является содержимым внутри контейнера. [Подробнее о базах данных, контейнерах и элементах.](databases-containers-items.md)

1. Получите URL-адрес конечной точки и первичный ключ на [портале Azure](https://portal.azure.com).

    На портале Azure перейдите к учетной записи Azure Cosmos DB, а затем щелкните **Ключи**.

    Скопируйте универсальный код ресурса (URI) с портала и вставьте его в параметр `<your endpoint URL>` в файле ```Program.cs```. Скопируйте на портале значение поля "Первичный ключ" и вставьте его в параметр `<your primary key>`.

   ![Снимок экрана получения ключей Azure Cosmos DB на портале Azure](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Далее мы создадим новый экземпляр ```CosmosClient``` и настроим формирование шаблонов для нашей программы.

    В методе **Main** добавьте новую асинхронную задачу с именем **GetStartedDemoAsync**, которая создает экземпляр ```CosmosClient```. Мы будем использовать **GetStartedDemoAsync** в качестве точки входа, которая вызывает методы для работы с ресурсами Azure Cosmos DB.

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

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Нажмите клавишу **F5**, чтобы запустить приложение. Результаты в окне консоли отображают сообщение `End of demo, press any key to exit.`. Это означает, что подключение к Azure Cosmos DB установлено. Теперь можно закрыть окно консоли.

Поздравляем! Вы успешно подключились к учетной записи Azure Cosmos DB. 

## <a name="step-4-create-a-database"></a>Шаг 4. Создание базы данных
Вы можете создать базу данных с помощью метода [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) или функции [**CreateDatabaseAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) класса ``CosmosClient``. База данных представляет собой логический контейнер элементов, разделенных между контейнерами.

1. Скопируйте и вставьте метод **CreateDatabaseAsync** ниже метода **GetStartedDemoAsync**. Метод **CreateDatabaseAsync** создаст новую базу данных (если она не была создана ранее) с идентификатором ``FamilyDatabase``, указанным в поле ``databaseId``. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Скопируйте и вставьте приведенный ниже код, где вы создали экземпляр CosmosClient, для вызова добавленного метода **CreateDatabaseAsync**.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    На этом этапе ваш код должен выглядеть следующим образом с заполненными значениями конечной точки и первичного ключа.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStarted
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
            private Database database;

            // The container we will create.
            private Container container;

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

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
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
>
>

Контейнер можно создать с помощью метода [**CreateContainerIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) или функции [**CreateContainerAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) из класса **CosmosDatabase**. Контейнер состоит из элементов (которые в случае API SQL являются документами JSON) и связанной логики приложения JavaScript на стороне сервера, например хранимых процедур, определяемых пользователем функций и триггеров.

1. Скопируйте и вставьте метод **CreateContainerAsync** ниже метода **CreateDatabaseAsync**. Функция **CreateContainerAsync** создаст новый контейнер (если он не был создан ранее) с идентификатором ``FamilyContainer``, указанным в поле ``containerId``, которое секционировано свойством ``LastName``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Скопируйте и вставьте приведенный ниже код, где вы создали экземпляр CosmosClient, для вызова добавленного метода **CreateContainer**.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

   Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно создали контейнер Azure Cosmos DB.  

## <a id="CreateDoc"></a>Шаг 6. Добавление элементов в контейнер
Элемент можно создать с помощью функции [**CreateItemAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainer) класса **CosmosContainer**. При использовании API SQL элементы проецируются как документы, которые являются содержимым JSON, определенным пользователем (произвольным). Теперь можно вставить элемент в ваш контейнер Azure Cosmos DB.

Сначала создадим класс **Family**, который будет представлять объекты, хранящиеся в Azure Cosmos DB в этом примере. Мы также создадим подклассы **Parent**, **Child**, **Pet** и **Address**, используемые в классе **Family**. Обратите внимание, что элемент должен иметь свойство **Id**, сериализуемое как **id** в файле JSON.

1. Выберите **Ctrl + Shift + A**, чтобы открыть диалоговое окно **Добавить новый элемент**. Добавьте в ваш проект новый класс **Family.cs**.

    ![Снимок экрана добавления в проект нового класса Family.cs](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Скопируйте и вставьте классы **Family**, **Parent**, **Child**, **Pet** и **Address** в **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Перейдите к файлу **Program.cs** и добавьте метод **AddItemsToContainerAsync** в свой метод **CreateContainerAsync**.
Перед созданием элемента код проверяет, нет ли элемента с таким же идентификатором. Мы вставим два элемента: по одному для семьи Андерсен и семьи Вейкфилд.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Добавьте вызов в ``AddItemsToContainerAsync`` в метод ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно создали два элемента Azure Cosmos DB.  

## <a id="Query"></a>Шаг 7. Запрашивание ресурсов Azure Cosmos DB
Azure Cosmos DB поддерживает [полнофункциональные запросы](sql-api-sql-query.md) к документам JSON, хранящимся в каждой коллекции. В следующем примере кода показано, как выполнить запрос к элементам, вставленным на предыдущем шаге.

1. Скопируйте и вставьте метод **QueryItemsAsync** ниже метода **AddItemsToContainerAsync**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Добавьте вызов в ``QueryItemsAsync`` в метод ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно выполнили запрос контейнера Azure Cosmos DB.

## <a id="ReplaceItem"></a>Шаг 8. Замена элемента JSON
Теперь мы будем обновлять элемент в Azure Cosmos DB.

1. Скопируйте и вставьте метод **ReplaceFamilyItemAsync** ниже метода **QueryItemsAsync**. Обратите внимание, что мы меняем свойство ``IsRegistered`` семейства и параметр ``Grade`` одного из дочерних элементов.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Добавьте вызов в ``ReplaceFamilyItemAsync`` в метод ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

   Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно заменили элемент Azure Cosmos DB.

## <a id="DeleteDocument"></a>Шаг 9. Удаление элемента
Теперь мы будем удалять элемент в Azure Cosmos DB.

1. Скопируйте и вставьте метод **DeleteFamilyItemAsync** ниже метода **ReplaceFamilyItemAsync**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Добавьте вызов в ``DeleteFamilyItemAsync`` в метод ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно удалили элемент Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Шаг 10. Удаление базы данных
Теперь удалим базу данных. Удаление созданной базы данных приведет к удалению базы данных и всех дочерних ресурсов (контейнеров, элементов и любых хранимых процедур, определяемых пользователем функций и триггеров). Мы также удалим экземпляр **CosmosClient**.

1. Скопируйте и вставьте метод **DeleteDatabaseAndCleanupAsync** ниже метода **DeleteFamilyItemAsync**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Добавьте вызов в ``DeleteDatabaseAndCleanupAsync`` в метод ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

Нажмите клавишу **F5**, чтобы запустить приложение.

Поздравляем! Вы успешно удалили базу данных Azure Cosmos DB.

## <a id="Run"></a>Шаг 11. Запуск консольного приложения C#
Чтобы создать приложение в режиме отладки, откройте Visual Studio и нажмите клавишу F5.

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
Если у вас нет времени на выполнение шагов из этого руководства или вы хотите просто скачать примеры кода, это можно сделать на портале [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). 

Чтобы создать решение GetStarted, требуются следующие компоненты.

* Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Учетная запись Azure Cosmos DB][cosmos-db-create-account].
* решение [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) , доступное в GitHub.

Чтобы в Visual Studio восстановить ссылки на пакет SDK .NET для Azure Cosmos DB, в обозревателе решений щелкните правой кнопкой мыши решение **GetStarted**, а затем выберите пункт **Восстановить пакеты NuGet**. Затем в файле App.config обновите значения EndPointUri и PrimaryKey согласно инструкциям, описанным в разделе о [подключении к учетной записи Azure Cosmos DB](#Connect).

Теперь все готово. Выполните сборку и начинайте работу с решением.

## <a name="next-steps"></a>Дополнительная информация
* Требуется более подробное руководство по ASP.NET MVC? См. [Руководство. Разработка веб-приложения ASP.NET MVC с использованием Azure Cosmos DB с помощью предварительной версии пакета SDK для .NET](sql-api-dotnet-application-preview.md).
* Хотите выполнять проверку масштабирования и производительности с помощью Azure Cosmos DB? См. [Проверка производительности и масштабирования с помощью Azure Cosmos DB](performance-testing.md)
* Узнайте, как организовать [мониторинг запросов, использования и хранилища Azure Cosmos DB](monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).
* Дополнительные сведения об Azure Cosmos DB см. в статье [Добро пожаловать в базу данных Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
