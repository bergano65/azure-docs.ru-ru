---
title: Руководство. Создание приложения .NET Core для управления данными, хранящимися в учетной записи API SQL для Azure Cosmos DB
description: В этом руководстве описано создание консольного приложения C# и оперативной базы данных с помощью пакета SDK для .NET Core API SQL в службе Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: 81c80dbd7ffa8e5c4e7b2ebb1c9d17eb6007ae9f
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802357"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>Руководство. Создание приложения .NET Core для управления данными, хранящимися в учетной записи SQL API

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (предварительная версия)](sql-api-dotnet-core-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET (предварительная версия)](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Как у разработчика у вас могут быть приложения, использующие данные документов NoSQL. Вы можете использовать учетную запись API SQL в Azure Cosmos DB для хранения этих данных документов и получения к ним доступа. В этом руководстве показано, как создать приложение .NET Core для создания данных, хранящихся в учетной записи API SQL в Azure Cosmos DB, и отправки запросов к ним. 

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * создание учетной записи Azure Cosmos и подключение к ней;
> * настройка решения Visual Studio;
> * Создание оперативной базы данных
> * создание коллекции;
> * создание документов JSON;
> * выполнение операций CRUD с элементами, контейнером и базой данных.

У вас нет времени на создание приложений? Полное решение доступно на [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). 

## <a name="prerequisites"></a>Предварительные требования

* Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Скачайте и используйте бесплатную версию [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Если вы разрабатываете приложения универсальной платформы Windows (UWP), используйте среду **Visual Studio 2017 версии 15.4** или более поздней версии. При установке Visual Studio необходимо включить возможность **разработки для Azure**.

    * Для MacOS или Linux можно разрабатывать приложения .NET Core с помощью командной строки, установив для этого пакет [SDK для .NET Core](https://www.microsoft.com/net/core#macos) с учетом платформы. 

    * Для Windows можно разрабатывать приложения .NET Core с помощью командной строки, установив для этого пакет [SDK для .NET Core](https://www.microsoft.com/net/core#windows). 

## <a name="create-an-azure-cosmos-account"></a>Создание учетной записи Azure Cosmos

Выполните следующие действия, чтобы создать учетную запись Azure Cosmos:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Настройка решения Visual Studio

1. Откройте **Visual Studio 2017** у себя на компьютере.

2. В меню **Файл** выберите пункт **Создать**, а затем — **Проект**.

3. В диалоговом окне **Новый проект** выберите **Шаблоны** > **Visual C#** > **.NET Core** > **Console Application (.NET Core)** (Консольное приложение (.NET Core)), а затем присвойте проекту имя **DocumentDBGettingStarted** и нажмите кнопку **ОК**.

   ![Снимок экрана с окном нового проекта](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. В **обозревателе решений** щелкните правой кнопкой мыши **DocumentDBGettingStarted**.

5. В том же меню выберите **Управление пакетами NuGet**.

   ![Снимок экрана: меню проекта, вызванное щелчком правой кнопки мыши](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. На вкладке **NuGet** в верхней части окна щелкните **Обзор** и в поле поиска введите **azure documentdb**. Убедитесь, что установлен флажок **Включить предварительные выпуски**.

7. В результатах найдите **Microsoft.Azure.DocumentDB.Core** и нажмите кнопку **Установить**.

   Идентификатором пакета для библиотеки является [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Если используется версия .NET Framework (например, net461), которая не поддерживается для этого пакета .NET Core NuGet, используйте [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB), поддерживающую все версии .NET Framework, начиная с .NET Framework 4.5.

8. При появлении запросов подтвердите установку пакета NuGet и примите условия лицензионного соглашения.

Теперь, когда настройка завершена, приступим к написанию кода. Вы можете найти проект готового кода для этого руководства в [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Подключение к учетной записи Azure Cosmos

Подключитесь к учетной записи Azure Cosmos, импортировав необходимые зависимости. Чтобы импортировать зависимости, добавьте следующий код в начало файла Program.cs:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

Затем добавьте эти две константы и переменную *client* в открытый класс *Program*.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Затем перейдите на [портал Azure](https://portal.azure.com), чтобы получить URI и первичный ключ. URI и первичный ключ Azure Cosmos DB позволяют приложению предоставлять данные о расположении, в котором будет устанавливаться подключение, делая подключение вашего приложения доверенным для Azure Cosmos DB.

На портале Azure перейдите к учетной записи Azure Cosmos, а затем щелкните **Ключи**.

Скопируйте универсальный код ресурса (URI) с портала и вставьте его в параметр `<your endpoint URI>` в файле program.cs. Затем скопируйте на портале значение поля "Первичный ключ" и вставьте его в параметр `<your key>`. Не забудьте удалить символы "<" и ">", но оставьте двойные кавычки, в которые заключены конечная точка и ключ.

![Получение ключей на портале Azure][keys]

Мы запустим ознакомительное приложение, создав экземпляр **DocumentClient**.

В методе **Main** добавьте эту новую асинхронную задачу с именем **GetStartedDemo**, которая создает экземпляр **DocumentClient**.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Добавьте указанный далее код, чтобы запустить асинхронную задачу из метода **Main** . Метод **Main** будет перехватывать исключения и записывать их в консоль.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

Нажмите кнопку **DocumentDBGettingStarted**, чтобы создать и запустить приложение.

## <a id="CreateDatabase"></a>Создание базы данных

Прежде чем добавлять код для создания базы данных, добавьте вспомогательный метод для записи на консоль. Скопируйте и вставьте метод **WriteToConsoleAndPromptToContinue** под кодом метода **GetStartedDemo**.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Создайте базу данных Azure Cosmos DB с использованием метода `CreateDatabaseAsync` в классе **DocumentClient**. База данных представляет собой логический контейнер для хранения документов JSON, разделенных между коллекциями. Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом создания клиента. Будет создана база данных с именем *FamilyDB*.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Нажмите кнопку **DocumentDBGettingStarted**, чтобы запустить приложение.

## <a id="CreateColl"></a>Создание коллекции

Создайте коллекцию с использованием метода `CreateDocumentCollectionAsync` в классе **DocumentClient**. Коллекция представляет собой контейнер документов JSON и связанную с ними логику в виде приложения JavaScript.

> [!WARNING]
> С использованием элемента **CreateDocumentCollectionAsync** можно создать коллекцию с зарезервированной пропускной способностью и соответствующей ценой. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cosmos-db/).

Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом создания базы данных. С помощью кода будет создана коллекция документов с именем *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Нажмите кнопку **DocumentDBGettingStarted**, чтобы запустить приложение.

## <a id="CreateDoc"></a>Создание документов JSON

Создайте документ с помощью метода `CreateDocumentAsync` в классе **DocumentClient**. Документы относятся к пользовательскому (произвольному) содержимому JSON. Теперь вы можете добавить один или несколько документов. 

Сначала создайте класс **Family**, который будет представлять объекты, хранящиеся в Azure Cosmos DB. Вы также создадите подклассы **Parent**, **Child**, **Pet** и **Address**, используемые в классе **Family**. У документов должно быть свойство **Id**, сериализуемое как **id** в файле JSON. Для этого после метода **GetStartedDemo** необходимо добавить следующие подклассы. Скопируйте и вставьте классы **Family**, **Parent**, **Child**, **Pet** и **Address** под кодом метода **WriteToConsoleAndPromptToContinue**.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
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

Скопируйте и вставьте метод **CreateFamilyDocumentIfNotExists** под кодом метода **CreateDocumentCollectionIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

Затем вставьте два документа, один для семьи Андерсен и один для семьи Вейкфилд. Скопируйте и вставьте код после `// ADD THIS PART TO YOUR CODE` в метод **GetStartedDemo** под кодом создания коллекции документов.

```csharp
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Нажмите кнопку **DocumentDBGettingStarted**, чтобы запустить приложение.

![Иерархические отношения между учетной записью, оперативной базой данных и коллекцией](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Запрос ресурсов Azure Cosmos DB

Azure Cosmos DB поддерживает полнофункциональные запросы к документам JSON, хранящимся в каждой коллекции. Ниже приведены примеры кода разнообразных запросов с использованием синтаксиса SQL в Azure Cosmos DB и LINQ, которые можно запускать для добавленных на предыдущем шаге документов.

Скопируйте и вставьте метод **ExecuteSimpleQuery** под кодом метода **CreateFamilyDocumentIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом создания второго документа.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Нажмите кнопку **DocumentDBGettingStarted**, чтобы запустить приложение.

На приведенной ниже схеме показан вызов синтаксиса SQL-запроса Azure Cosmos DB к созданной коллекции. Эта же логика применяется к запросам LINQ.

![На схеме представлен масштаб и значение запроса, используемого в руководстве по NoSQL при создании консольного приложения C#.](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

Ключевое слово `FROM` использовать в запросе необязательно, так как запросы Azure Cosmos DB привязаны только к одной коллекции. Поэтому слова "FROM Families f" можно заменить на "FROM root r" или любое другое имя переменной. Поведение службы Azure Cosmos DB будет таким, будто Families, root или любая другая переменная указывает на текущую коллекцию по умолчанию.

## <a id="ReplaceDocument"></a>Замена документа JSON

Azure Cosmos DB поддерживает замену документов JSON.  

Скопируйте и вставьте метод **ReplaceFamilyDocument** под кодом метода **ExecuteSimpleQuery**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом выполнения запроса. После замены документа тот же запрос будет запущен повторно, чтобы вы могли просмотреть измененный документ.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Нажмите кнопку **DocumentDBGettingStarted**, чтобы запустить приложение.

## <a id="DeleteDocument"></a>Удаление документа JSON

Azure Cosmos DB поддерживает удаление документов JSON.  

Скопируйте и вставьте метод **DeleteFamilyDocument** под кодом метода **ReplaceFamilyDocument**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом выполнения второго запроса.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Нажмите кнопку **DocumentDBGettingStarted**, чтобы запустить приложение.

## <a id="DeleteDatabase"></a>Удаление базы данных

Удаление созданной базы данных приведет к удалению базы данных и всех дочерних ресурсов (коллекций, документов и т. д.). Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом документа, чтобы удалить всю базу данных и все дочерние ресурсы.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Нажмите кнопку **DocumentDBGettingStarted**, чтобы запустить приложение.

## <a id="Run"></a>Запуск консольного приложения C#

В Visual Studio нажмите кнопку **DocumentDBGettingStarted**, чтобы создать приложение в режиме отладки. В окне консоли должны отобразиться выходные данные вашего приложения. Они должны содержать результаты обработки добавленных запросов. При этом выглядеть они должны примерно так, как показано в примере ниже.

```bash
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

Вы завершили работу с этим руководством, и теперь у вас есть работающее консольное приложение C#.

## <a name="clean-up-resources"></a>Очистка ресурсов

Можно удалить группу ресурсов, учетную запись Azure Cosmos и все связанные ресурсы, когда они больше не нужны. Для этого выберите группу ресурсов для виртуальной машины, выберите **Удалить** и подтвердите имя удаляемой группы ресурсов.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как создать приложение .NET Core для управления данными, хранимыми в учетной записи API SQL в Azure Cosmos DB. Теперь вы можете перейти к следующей статье:

> [!div class="nextstepaction"]
> [Руководство по NoSQL. Создание консольного приложения Java с помощью API-интерфейса SQL](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
