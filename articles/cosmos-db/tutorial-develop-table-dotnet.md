---
title: Начало работы с API таблиц Azure Cosmos DB с помощью пакета SDK для .NET Standard
description: Хранение структурированных данных в облаке с помощью API таблиц Azure Cosmos DB.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 05/20/2019
ms.openlocfilehash: 677ea48244f8417670a2645ab67fa08c1f869f1a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142586"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Начало работы с API таблиц Azure Cosmos DB и хранилищем таблиц Azure с помощью пакета SDK для .NET

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

API таблиц Azure Cosmos DB или хранилище таблиц Azure можно использовать для хранения структурированных NoSQL-данных в облаке, предоставляя хранилище для ключей и атрибутов с бессхемной структурой. Так как API таблиц Azure Cosmos DB и хранилище таблиц являются бессхемными, это позволяет легко адаптировать данные по мере роста приложения. API таблиц Azure Cosmos DB или хранилище таблиц можно использовать для хранения гибких наборов данных, например пользовательских данных для веб-приложений, адресных книг, сведений об устройстве или метаданных любого другого типа, которые требуются вашей службе. 

В этом руководстве описано, как использовать [библиотеки таблиц Azure Cosmos DB для .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) в сценариях работы с API таблиц Azure Cosmos DB и хранилища таблиц Azure. Необходимо использовать подключение, связанное с определенной службой Azure. Эти сценарии рассматриваются с использованием примеров на C#, которые демонстрируют методы создания таблиц, вставки и обновления данных, запроса данных и удаления таблиц.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим примером требуются следующие компоненты:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Библиотека таблиц Microsoft Azure CosmosDB для .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Эта библиотека сейчас доступна для платформ .NET Standard и .NET. 

* [Учетная запись API таблиц Azure Cosmos DB](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Создание учетной записи API таблиц Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Создайте консольный проект .NET

Создайте консольное приложение .NET в Visual Studio. Ниже показано, как создать консольное приложение в Visual Studio 2019. Вы можете использовать библиотеку таблиц Azure Cosmos DB в любом приложении .NET, в том числе облачной службе Azure, веб-приложении Azure, а также классическом или мобильном приложении. Для упрощения в этом руководстве мы будем использовать консольное приложение.

1. Выберите **Файл** > **Создать** > **Проект**.

1. Выберите **Консольное приложение (.NET Core)** , а затем щелкните **Далее**.

1. В поле **Имя проекта** введите имя для приложения, например **CosmosTableSamples**. (При необходимости можно указать другое имя).

1. Нажмите кнопку **Создать**.

Все примеры кода из этого примера можно добавить в метод Main() в файле **Program.cs** консольного приложения.

## <a name="install-the-required-nuget-package"></a>Установка необходимых пакетов NuGet

Чтобы получить пакеты NuGet, сделайте следующее.

1. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**.

1. Выполните поиск в Интернете по запросу `Microsoft.Azure.Cosmos.Table`, `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json`, `Microsoft.Extensions.Configuration.Binder` и выберите **Установить**, чтобы установить библиотеку таблиц Microsoft Azure Cosmos DB.

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища

1. На [портале Azure](https://portal.azure.com/) перейдите к учетной записи Azure Cosmos или Хранилища таблиц. 

1. Откройте панель **Строка подключения** или **Ключи доступа**. Используйте кнопку копирования в правой части окна, чтобы скопировать **основную строку подключения**.

   ![Просмотр и копирование основной строки подключения на панели строки подключения](./media/create-table-dotnet/connection-string.png)
   
1. Чтобы настроить строку подключения, щелкните в Visual Studio правой кнопкой мыши проект **CosmosTableSamples**.

1. Выберите **Добавить**, а затем **Новый элемент**. Создайте файл **Settings.json** с типом файла **Файл конфигурации TypeScript JSON**. 

1. Замените код в файл Settings.json следующим кодом и укажите основную строку подключения.

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Щелкните проект **CosmosTableSamples** правой кнопкой мыши. Выберите **Добавить**, **Новый элемент** и добавьте класс с именем **AppSettings.cs**.

1. Добавьте в файл AppSettings.cs указанный ниже код. Этот файл считывает строку подключения из файла Settings.json и назначает ее значение параметру конфигурации:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>Синтаксический анализ и проверка сведений о подключении 

1. Щелкните проект **CosmosTableSamples** правой кнопкой мыши. Выберите **Добавить**, **Новый элемент** и добавьте класс с именем **Common.cs**. В этом классе вы создадите код для проверки сведений о подключении и создания таблицы.

1. Определите метод `CreateStorageAccountFromConnectionString`, как показано ниже. Этот метод будет анализировать данные строки подключения и проверять допустимость для имени учетной записи и ключа учетной записи, которые указаны в файле Settings.json. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>Создание таблицы 

Класс [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) позволяет получать таблицы и сущности, хранящиеся в хранилище таблиц. Так как в учетной записи API таблиц Cosmos DB пока нет ни одной таблицы, давайте добавим метод `CreateTableAsync` для их создания в класс **Common.cs**:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

## <a name="define-the-entity"></a>Определение сущности 

Сущности сопоставляются с объектами C# с помощью настраиваемого класса, производного от [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Чтобы добавить сущность в таблицу, создайте класс, который определяет свойства сущности.

Щелкните проект **CosmosTableSamples** правой кнопкой мыши. Выберите **Добавить**, **Новая папка** и присвойте ей имя **Model**. В папку Model добавьте класс с именем **CustomerEntity.cs** и добавьте следующий код.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Этот код определяет класс сущностей, который использует в качестве ключа строки имя клиента, а в качестве ключа раздела — фамилию клиента. Вместе ключ раздела и ключ строки сущности уникальным образом идентифицируют сущность в таблице. Сущности с одинаковым ключом раздела возвращаются быстрее, чем с разными ключами раздела, но использование разных ключей разделов повышает масштабируемость параллельных операций. В таблицах можно хранить только сущности поддерживаемых типов, например производных от класса [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Свойства сущности, которые вы хотите хранить в таблице, должны быть открытыми свойствами типа и поддерживать получение и настройку значений. Кроме того, тип сущности должен предоставлять конструктор без параметров.

## <a name="insert-or-merge-an-entity"></a>Вставка или слияние сущностей

Следующий пример кода создает объект сущности и добавляет его в таблицу. Метод InsertOrMerge из класса [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) используется для вставки или слияния сущностей. Для выполнения этой операции вызывается метод [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet). 

Щелкните проект **CosmosTableSamples** правой кнопкой мыши. Выберите **Добавить**, **Новый элемент** и добавьте класс с именем **SamplesUtils.cs**. Этот класс содержит весь необходимый код для выполнения операций CRUD для сущностей. 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

### <a name="get-an-entity-from-a-partition"></a>Получение сущности из раздела

Чтобы получить сущность из раздела, можно применить метод Retrieve из класса [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation). Следующий пример кода получает ключ раздела, ключ строки, адрес электронной почты и номер телефона из сущности customer. Также этот пример возвращает сведения о количестве единиц запросов, затраченных на запрос сущности. Чтобы запросить данные сущности, добавьте следующий код в файл **SamplesUtils.cs**: 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Удаление сущности

Сущность можно легко удалить после ее получения с использованием того же шаблона, что и для обновления сущностей. Следующий код извлекает и удаляет сущность клиента. Чтобы удалить сущность, добавьте следующий код в файл **SamplesUtils.cs**: 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>Выполнение операций CRUD для тестовых данных

Определив методы для создания таблицы, вставки и слияния сущностей, примените эти методы для тестовых данных. Для этого щелкните проект **CosmosTableSamples** правой кнопкой мыши. Выберите **Добавить**, **Новый элемент**, добавьте класс с именем **BasicSamples.cs** и добавьте следующий код. Этот код создает таблицу и добавляет в нее сущности. Если вам нужно удалить эту сущность и таблицу после завершения проекта, удалите комментарии из методов `table.DeleteIfExistsAsync()` и `SamplesUtils.DeleteEntityAsync(table, customer)` в следующем коде.

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

Приведенный выше код создает таблиц с именем, состоящим из префикса demo и созданного идентификатора GUID. Затем он добавляет сущность customer, задав для нее имя и фамилию Walter Harp, а затем изменяет номер телефона для этого пользователя. 

В этом руководстве описано, как создать код, который позволяет выполнять основные операции CRUD с данными, хранящимися в учетной записи API таблиц. Вы также можете выполнять дополнительные операции, например пакетную вставку данных, получение всех данных из раздела, запрос диапазона данных в разделе или получение списка таблиц в учетной записи, имена которых начинаются с указанного префикса. Полный пример [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) вы можете скачать из репозитория GitHub. Класс [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) поддерживает ряд дополнительных операций, которые можно выполнять с данными.  

## <a name="run-the-project"></a>Запуск проекта

В проекте **CosmosTableSamples** откройте класс с именем **Program.cs** и добавьте следующий код для вызова BasicSamples при запуске проекта.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Теперь скомпилируйте решение и запустите проект, нажав клавишу F5. При выполнении проекта вы увидите в командной строке следующие выходные данные:

![Выходные данные в командной строке](./media/tutorial-develop-table-standard/output-from-sample.png)

Если возникнет ошибка с сообщением о том, что при запуске проекта файл Settings.json не удалось найти, эту ошибку можно устранить, добавив в настройки проекта следующую XML-запись. Щелкните правой кнопкой мыши CosmosTableSamples, выберите "Редактировать CosmosTableSamples.csproj" и добавьте следующий элемент itemGroup. 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Теперь можно войти на портал Azure и проверить, появились ли данные в таблице. 

![Результаты на портале](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Дополнительная информация

Теперь вы можете перейти к следующему руководству, из которого вы узнаете, как перенести данные в учетную запись API таблиц Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Сведения о запросе данных](../cosmos-db/table-import.md)
