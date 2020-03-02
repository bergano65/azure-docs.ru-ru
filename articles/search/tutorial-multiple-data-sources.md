---
title: C#Учебник. индексирование нескольких источников данных
titleSuffix: Azure Cognitive Search
description: Узнайте, как импортировать данные из нескольких источников данных в один индекс Когнитивный поиск Azure с помощью индексаторов. Этот учебник и пример кода находятся в C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 272926e6c3572f03cc316ee696893941fd91968d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206883"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>Учебник. индексирование данных из нескольких источников данных вC#

Azure Когнитивный поиск может импортировать, анализировать и индексировать данные из нескольких источников данных в единый индекс поиска консолидации. Это нужно для тех ситуаций, когда структурированные данные объединяются с менее структурированными данными или даже простым текстом из других источников, таких как текстовые документы, код HTML и JSON.

Этот учебник описывает, как индексировать данные об отелях из источника данных Azure Cosmos DB и как объединить их с подробными сведениями о номерах отелей, извлеченными из документов в хранилище BLOB-объектов. В результате мы получим индекс поиска данных об отелях со сложными типами данных.

В этом руководстве C# используется и [пакет SDK для .NET](https://aka.ms/search-sdk). В этом руководстве вы выполните следующие задачи:

> [!div class="checklist"]
> * отправка примера данных и создание источников данных;
> * Определение ключа документа
> * определение и создание индекса;
> * индексирование данных об отелях из Azure Cosmos DB;
> * объединение данных о номерах отелей из хранилища BLOB-объектов.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Хранилище Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Создание](search-create-service-portal.md) или [Поиск существующей службы поиска](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Для работы с этим руководством можно использовать бесплатную службу. Бесплатная служба поиска ограничивает вас тремя индексами, тремя индексаторами и тремя источниками данных. В этом руководстве создается по одному объекту из каждой категории. Прежде чем начать, убедитесь, что в службе есть место для принятия новых ресурсов.

## <a name="download-files"></a>Загрузка файлов

Исходный код для этого руководства находится в репозитории GitHub [Azure-Search-DotNet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) в папке с [несколькими источниками данных](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources) .

## <a name="1---create-services"></a>1\. Создание служб

В этом руководстве используется Когнитивный поиск Azure для индексирования и запросов, Azure Cosmos DB для одного набора данных и хранилища BLOB-объектов Azure для второго набора данных. 

По возможности создавайте все службы в одном регионе и группе ресурсов для сходства и управляемости. На практике службы могут находиться в любом регионе.

В этом примере используются два небольших набора данных, которые описывают семь вымышленных отелей. Один набор, который описывает сами отели, мы загрузим в базу данных Azure Cosmos DB. Другой набор с подробными сведениями о номерах в этих отелях предоставляется в виде семи отдельных файлов формата JSON, и его мы отправим в хранилище BLOB-объектов Azure.

### <a name="start-with-cosmos-db"></a>Начало работы с Cosmos DB

1. Войдите в [портал Azure](https://portal.azure.com), а затем перейдите на страницу обзора учетной записи Azure Cosmos DB.

1. Выберите **Обозреватель данных** и щелкните **Новая база данных**.

   ![Создание новой базы данных](media/tutorial-multiple-data-sources/cosmos-newdb.png "Создать новую базу данных")

1. Введите имя **отеля-комнаты-DB**. Примите значения по умолчанию для остальных параметров.

   ![Настройка базы данных](media/tutorial-multiple-data-sources/cosmos-dbname.png "Настройка базы данных")

1. Создайте новый контейнер. Используйте только что созданную базу данных. Введите **Гостиницы** для имени контейнера и используйте **/Хотелид** для ключа секции.

   ![Добавить контейнер](media/tutorial-multiple-data-sources/cosmos-add-container.png "Добавление контейнера")

1. Выберите **элементы** в разделе **Гостиницы**, а затем щелкните **отправить элемент** на панели команд. Перейдите в папку проекта и выберите файл **cosmosdb/HotelsDataSubset_CosmosDb. JSON** .

   ![Отправка в коллекцию Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Отправка в коллекцию Cosmos DB")

1. С помощью кнопки "Обновить" освежите представление элементов в коллекции hotels. Вы увидите в списке семь новых документов базы данных.

### <a name="azure-blob-storage"></a>Хранилище BLOB-объектов Azure

1. Войдите в [портал Azure](https://portal.azure.com), перейдите к своей учетной записи хранения Azure, щелкните **BLOB-объекты**, а затем — **+ контейнер**.

1. [Создайте контейнер больших двоичных объектов](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) с именем **hotel-rooms**, в котором будут храниться JSON-файлы с примерами данных о номерах отелей. Можно задать любое из допустимых значений уровня общего доступа.

   ![Создание контейнера больших двоичных объектов](media/tutorial-multiple-data-sources/blob-add-container.png "Создание контейнера BLOB-объектов")

1. Откройте контейнер после создания и на панели команд выберите **Загрузить**. Перейдите к папке, содержащей примеры файлов. Выберите их, а затем щелкните **Загрузить**.

   ![Передача файлов](media/tutorial-multiple-data-sources/blob-upload.png "Upload files")

Когда отправка файлов завершится, они появятся в списке внутри контейнера данных.

### <a name="azure-cognitive-search"></a>Когнитивный поиск Azure

Третий компонент — Когнитивный поиск Azure, который вы можете [создать на портале](search-create-service-portal.md). Для выполнения действий в этом пошаговом руководстве можно использовать уровень "Бесплатный". 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Получение ключа API и URL-адреса конечной точки для администрирования Когнитивного поиска Azure

Для взаимодействия со службой "Когнитивный поиск Azure" вам нужны URL-адрес службы и ключ доступа. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Когнитивный поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

   Получите также ключ запроса. Мы рекомендуем создавать запросы с доступом только для чтения.

   ![Получение имени службы, ключей запросов и администратора](media/search-get-started-nodejs/service-name-and-keys.png)

Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="2---set-up-your-environment"></a>2\. Настройка среды

1. Запустите Visual Studio 2019 и в меню **Сервис** выберите **Диспетчер пакетов NuGet** , а затем — **Управление пакетами NuGet для решения..** .. 

1. На вкладке **Просмотреть** найдите и установите **Microsoft.Azure.Search** (версия 9.0.1 или более поздняя). Для завершения установки нужно пройти серию дополнительных диалоговых окон.

    ![Добавление библиотек Azure с помощью NuGet](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Найдите пакет NuGet **Microsoft. Extensions. Configuration. JSON** и установите его.

1. Откройте файл решения **азуресеарчмултипледатасаурцес. sln**.

1. В обозреватель решений измените файл **appSettings. JSON** , чтобы добавить сведения о соединении.  

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "BlobStorageAccountName": "Put your Azure Storage account name here",
      "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
      "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

Первые две записи содержат URL-адрес и ключи администратора для службы "Когнитивный поиск Azure". Например, если конечная точка имеет имя`https://mydemo.search.windows.net`, следует указать имя службы `mydemo`.

Следующие записи содержат имена учетных записей и строки подключения для хранилища BLOB-объектов Azure и источников данных Azure Cosmos DB.

## <a name="3---map-key-fields"></a>3\. сопоставленные ключевые поля

Для объединения содержимого необходимо, чтобы оба потока данных насовпадали с теми же документами в индексе поиска. 

В Azure Когнитивный поиск ключевое поле однозначно определяет каждый документ. Каждый индекс поиска должен содержать только одно поле ключа типа `Edm.String`. Это поле ключа должно присутствовать в источнике данных для каждого документа, который добавляется к индексу. (Фактически, это единственное обязательное для заполнения поле.)

При индексировании данных из нескольких источников данных убедитесь, что каждая входящая строка или документ содержит общий ключ документа для слияния данных из двух физически различных исходных документов в новый документ поиска в комбинированном индексе. 

Часто требуется несколько предварительных планирований для обнаружения осмысленного ключа документа для индекса и убедитесь, что он существует в обоих источниках данных. В этой демонстрации ключ `HotelId` для каждого Гостиницы в Cosmos DB также содержится в больших двоичных объектах хранилища JSON в хранилище BLOB-объектов.

Индексаторы службы "Когнитивный поиск Azure" умеют использовать сопоставления полей для изменения имен и форматов полей данных в процессе индексирования, чтобы правильно сопоставить данные из источника с нужным полем индекса. Например, в Cosmos DB идентификатор отеля называется **`HotelId`** . Но в файлах больших двоичных объектов JSON для комнат отеля идентификатор отеля называется **`Id`** . Программа обрабатывает это путем сопоставления поля **`Id`** из больших двоичных объектов с **`HotelId`ным** полем ключа в индексе.

> [!NOTE]
> В большинстве случаев автоматически создаваемые ключи документов, такие как созданные по умолчанию некоторыми индексаторами, не дают хороших ключей документов для Объединенных индексов. Обычно вам нужны содержательные и уникальные значения ключей, которые уже существуют в источниках данных и (или) легко могут быть добавлены к ним.

## <a name="4---explore-the-code"></a>4\. Изучение кода

Когда вы завершите отправку данных и настройку параметров конфигурации, пример программы **AzureSearchMultipleDataSources.sln** будет готов к сборке и запуску.

Это простое консольное приложение для C#/.NET выполняет следующие задачи:

* Создает новый индекс на основе структуры данных класса C# отеля (который также ссылается на классы Address и Room).
* Создает новый источник данных и индексатор, который сопоставляет Azure Cosmos DB данные с полями индекса. Это объекты в Azure Когнитивный поиск.
* Запускает индексатор для загрузки данных отеля из Cosmos DB.
* Создает второй источник данных и индексатор, который сопоставляет данные JSON BLOB с полями индекса.
* Запускает второй индексатор для загрузки данных комнат из хранилища BLOB-объектов.

 Перед выполнением этой программы вам стоит изучить ее код и определения индекса и индексатора, настроенные для нашего примера. Соответствующий код находится в двух файлах:

  + **Hotel.cs** содержит схему, которая определяет индекс;
  + **Program.cs** содержит функции, которые создают индекс службы "Когнитивный поиск Azure", источники данных и индексаторы, а также отправляют в индекс объединенные результаты.

### <a name="create-an-index"></a>Создание индекса

Этот пример программы использует пакет SDK для .NET, чтобы определить и создать индекс службы "Когнитивный поиск Azure". С помощью [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) из класса C# модели данных программа создает структуру индекса.

Модель данных определяется в классе Hotel, который содержит также ссылки на классы Address и Room. FieldBuilder углубляется в структуру определений нескольких классов, создавая на их основе сложную структуру данных для индекса. Теги метаданных применяются для определения атрибутов каждого поля, например сведений о поддержке поиска или сортировки.

Следующие фрагменты кода из файла **Hotel.cs** демонстрируют, как можно определить одно поле и ссылку на другой класс модели данных.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

В файле **Program.cs** для индекса определяется имя и коллекция полей, созданных методом `FieldBuilder.BuildForType<Hotel>()`, а затем этот индекс создается, как показано ниже:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Создание источника данных и индексатора Azure Cosmos DB

Далее основая программа включает логику для создания источника данных Azure Cosmos DB, где будут храниться данные об отелях.

Сначала она объединяет имя базы данных Azure Cosmos DB и строку подключения. Затем она определяет объект источника данных, в том числе особые параметры для источников Azure Cosmos DB, например свойство [useChangeDetection].

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

После создания источника данных программа настраивает индексатор Azure Cosmos DB с именем **hotel-rooms-cosmos-indexer**.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
Перед созданием индексатора программа также удаляет существующий индексатор с тем же именем на случай, если вы будете выполнять этот пример более одного раза.

Этот пример определяет для индексатора расписание, согласно которому он будет выполняться один раз в день. Вы можете удалить из вызова свойство расписания, если вам не нужно автоматически выполнять индексатор в будущем.

### <a name="index-azure-cosmos-db-data"></a>Индексация данных Azure Cosmos DB

После создания источника данных и индексатора вы можете выполнить этот индексатор с помощью следующего краткого кода:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Этот пример содержит простой блок try-catch, чтобы информировать о любых ошибках в процессе выполнения.

После выполнения индексатора Azure Cosmos DB полученный индекс поиска будет содержать полный набор документов с примерами данных об отелях. Но при этом поле rooms для каждого отеля будет содержать пустой массив, поскольку источник данных Azure Cosmos DB не содержит сведения о номерах. После этого программа выполняет запрос к хранилищу BLOB-объектов на загрузку данных о номерах для объединения с остальными данными.

### <a name="create-blob-storage-data-source-and-indexer"></a>Создание источника данных и индексатора хранилища BLOB-объектов

Чтобы получить сведения о номерах, программа прежде всего настраивает хранилище BLOB-объектов в качестве источника данных, из которого будет использовать набор JSON-файлов с большими двоичными объектами.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

После создания источника данных программа настраивает для него индексатор с именем **hotel-rooms-blob-indexer**.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

Большие двоичные объекты JSON содержат ключевое поле с именем **`Id`** , а не **`HotelId`** . В коде используется класс `FieldMapping`, чтобы указать индексатору направить значение поля **`Id`** в **`HotelId`** ключ документа в индексе.

Индексаторы хранилища больших двоичных объектов умеют использовать параметры, определяющие режим анализа. Режим анализа будет разным для больших двоичных объектов, представляющих один документ или несколько документов. В нашем примере каждый большой двоичный объект представляет один документ индекса, поэтому в коде используется параметр `IndexingParameters.ParseJson()`.

Дополнительные сведения о параметрах индексатора для анализа больших двоичных объектов JSON см. в [этой статье](search-howto-index-json-blobs.md). Дополнительные сведения о том, как указать эти параметры с помощью пакета SDK для .NET, см. в документации по классу [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions).

Перед созданием индексатора программа также удаляет существующий индексатор с тем же именем на случай, если вы будете выполнять этот пример более одного раза.

Этот пример определяет для индексатора расписание, согласно которому он будет выполняться один раз в день. Вы можете удалить из вызова свойство расписания, если вам не нужно автоматически выполнять индексатор в будущем.

### <a name="index-blob-data"></a>Индексирование данных в больших двоичных объектах

После создания источника данных и индексатора в хранилище BLOB-объектов вы можете выполнить этот индексатор с помощью следующего краткого кода:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Так как индекс уже был заполнен данными об отелях из базы данных Azure Cosmos DB, индексатор больших двоичных объектов просто обновляет существующие документы в индексе, добавляя в них сведения о номерах.

> [!NOTE]
> Если в обоих источниках данных есть одинаковые поля, не являющиеся ключевыми, и данные в этих полях не совпадают, то итоговый индекс будет содержать значения от того индексатора, который выполнялся последним. В нашем примере оба источники данных содержат поля **HotelName**. Если по какой-либо причине данные в этом поле различаются, то для документов с одинаковым значением ключа будет сохранено значение **HotelName** из того источника данных, который был проиндексирован позднее.

## <a name="5---search"></a>5\. Поиск

Вы можете изучить заполненный индекс поиска на портале после выполнения программы с помощью компонента [**Обозреватель поиска**](search-explorer.md).

На портале Azure откройте страницу **Обзор** для службы поиска и найдите в списке **Индексы** созданный индекс **hotel-rooms-sample**.

  ![Список индексов службы "Когнитивный поиск Azure"](media/tutorial-multiple-data-sources/index-list.png "Список индексов службы "Когнитивный поиск Azure"")

Щелкните индекс hotel-rooms-sample в этом списке. Вы увидите интерфейс обозревателя поиска для этого индекса. Введите запрос для поиска термина, например Luxury. Вы увидите результат поиска с хотя бы одним документом, в котором отображается список объектов номеров из массива rooms.

## <a name="reset-and-rerun"></a>Сброс и повторный запуск

На ранних стадиях разработки наиболее практичный подход к итерации проекта заключается в удалении объектов из Azure Когнитивный поиск и разрешении коду перестраивать их. Имена ресурсов являются уникальными. Удаление объекта позволяет воссоздать его с использованием того же имени.

Пример кода для этого руководства проверяет наличие существующих объектов и удаляет их, чтобы можно было повторно запустить код.

Можно также использовать портал для удаления индексов, индексаторов и источников данных.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы работаете в своей подписке, после завершения проекта целесообразно удалить созданные ресурсы, которые вам больше не потребуются. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Найти ресурсы и управлять ими можно на портале, используя ссылку все ресурсы или группы ресурсов в области навигации слева.

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы знакомы с концепцией приема данных из нескольких источников, давайте подробнее рассмотрим конфигурацию индексатора, начиная с Cosmos DB.

> [!div class="nextstepaction"]
> [Настройка индексатора Azure Cosmos DB](search-howto-index-cosmosdb.md)