---
title: Руководство по C#. Индексирование нескольких источников данных Azure
titleSuffix: Azure Cognitive Search
description: Узнайте, как импортировать данные из нескольких источников данных в один индекс службы "Когнитивный поиск Azure" с помощью индексаторов. В этом учебнике и примере кода используется язык C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: d63e437090b2875c7e6a8273fdf22d49597d408f
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2020
ms.locfileid: "85262214"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Руководство по Индексирование из нескольких источников данных с помощью пакета SDK для .NET

Когнитивный поиск Azure позволяет импортировать, анализировать и индексировать данные из нескольких источников данных в объединенный индекс для поиска. Это нужно для тех ситуаций, когда структурированные данные объединяются с менее структурированными данными или даже простым текстом из других источников, таких как текстовые документы, код HTML и JSON.

Этот учебник описывает, как индексировать данные об отелях из источника данных Azure Cosmos DB и как объединить их с подробными сведениями о номерах отелей, извлеченными из документов в хранилище BLOB-объектов. В результате мы получим индекс поиска данных об отелях со сложными типами данных.

В этом учебнике используется язык C# и [пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search). Вот какие шаги выполняются в этом учебнике:

> [!div class="checklist"]
> * отправка примера данных и создание источников данных;
> * Определение ключа документа
> * определение и создание индекса;
> * индексирование данных об отелях из Azure Cosmos DB;
> * объединение данных о номерах отелей из хранилища BLOB-объектов.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Хранилище Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Создайте службу поиска](search-create-service-portal.md) или [найдите существующую службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Для выполнения инструкций из этого руководства вы можете использовать бесплатную версию службы. В бесплатной версии вы можете использовать не более трех индексов, трех индексаторов и трех источников данных. В этом руководстве создается по одному объекту из каждой категории. Перед началом работы убедитесь, что у службы есть достаточно места, чтобы принять новые ресурсы.

## <a name="download-files"></a>Загрузка файлов

Исходный код этого учебника расположен в репозитории GitHub [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) в папке [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources).

## <a name="1---create-services"></a>1\. Создание служб

В этом учебнике используется служба "Когнитивный поиск Azure" для индексирования и запросов, Azure Cosmos DB — для первого набора данных и хранилище BLOB-объектов Azure — для второго. 

Желательно создать все службы в одном регионе и в одной группе ресурсов, чтобы упростить взаимодействие и управление. На практике службы могут находиться в любом регионе.

В этом примере используются два небольших набора данных, которые описывают семь вымышленных отелей. Один набор, который описывает сами отели, мы загрузим в базу данных Azure Cosmos DB. Другой набор с подробными сведениями о номерах в этих отелях предоставляется в виде семи отдельных файлов формата JSON, и его мы отправим в хранилище BLOB-объектов Azure.

### <a name="start-with-cosmos-db"></a>Начало работы с Cosmos DB

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к странице обзора для учетной записи Azure Cosmos DB.

1. Выберите раздел **Обозреватель данных**, а затем — команду **Новая база данных**.

   ![Создание базы данных](media/tutorial-multiple-data-sources/cosmos-newdb.png "Создание базы данных")

1. Введите имя **hotel-rooms-db**. Примите значения по умолчанию для остальных параметров.

   ![Настройка базы данных](media/tutorial-multiple-data-sources/cosmos-dbname.png "Настройка базы данных")

1. Создайте новый контейнер. Используйте имеющуюся базу данных, которую вы только что создали. В качестве имени контейнера введите **hotels**, а имени ключа секции — **/HotelId**.

   ![Добавление контейнера](media/tutorial-multiple-data-sources/cosmos-add-container.png "Добавление контейнера")

1. В разделе **hotels** выберите **Элементы**, а затем на панели команд щелкните **Upload Item** (Отправить элемент). Перейдите к файлу **cosmosdb/HotelsDataSubset_CosmosDb.json** в папке проекта и выберите его.

   ![Отправка в коллекцию Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Отправка в коллекцию Cosmos DB")

1. С помощью кнопки "Обновить" освежите представление элементов в коллекции hotels. Вы увидите в списке семь новых документов базы данных.

### <a name="azure-blob-storage"></a>Хранилище BLOB-объектов Azure

1. Войдите на [портал Azure](https://portal.azure.com), перейдите к учетной записи хранения Azure, щелкните **Большие двоичные объекты**, а затем — **+ Container** (+ Контейнер).

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

## <a name="2---set-up-your-environment"></a>2\. Настройка среды

1. Запустите Visual Studio 2019. В меню **Средства** выберите **Диспетчер пакетов NuGet**, а затем **Управление пакетами NuGet для решения...** . 

1. На вкладке **Просмотреть** найдите и установите **Microsoft.Azure.Search** (версия 9.0.1 или более поздняя). Для завершения установки нужно пройти серию дополнительных диалоговых окон.

    ![Добавление библиотек Azure с помощью NuGet](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Найдите пакет NuGet **Microsoft.Extensions.Configuration.Json** и установите его.

1. Откройте файл решения **AzureSearchMultipleDataSources.sln**.

1. В обозревателе решений измените файл **appsettings.json**, добавив сведения о подключении.  

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

## <a name="3---map-key-fields"></a>3\. Сопоставление полей ключей

Для объединения содержимого требуется, чтобы оба потока данных использовали одни и те же документы в индексе поиска. 

В службе "Когнитивный поиск Azure" поле ключа однозначно определяет каждый документ. Каждый индекс поиска должен содержать только одно поле ключа типа `Edm.String`. Это поле ключа должно присутствовать в источнике данных для каждого документа, который добавляется к индексу. (Фактически, это единственное обязательное для заполнения поле.)

При индексировании данных из нескольких источников данных убедитесь, что каждая входная строка или входной документ содержит общий ключ документа для объединения данных из двух физически различных исходных документов в новый документ поиска в комбинированном индексе. 

Зачастую приходится потратить некоторое время, чтобы заранее составить разумный ключ документа для создаваемого индекса и убедиться, что он существует в обоих источниках данных. В этом демонстрационном примере ключ `HotelId` для каждого отеля в Cosmos DB также присутствует в больших двоичных объектах номеров в формате JSON в хранилище BLOB-объектов.

Индексаторы службы "Когнитивный поиск Azure" умеют использовать сопоставления полей для изменения имен и форматов полей данных в процессе индексирования, чтобы правильно сопоставить данные из источника с нужным полем индекса. Например, в Cosmos DB идентификатор отеля называется **`HotelId`** . Но в файлах JSON больших двоичных объектов с информацией о номерах отелей этот идентификатор именуется **`Id`** . Программа решает эту проблему, сопоставляя поля **`Id`** из больших двоичных объектов c полем ключа **`HotelId`** в индексе.

> [!NOTE]
> В большинстве случаев автоматически сформированные ключи документов, например стандартные ключи некоторых индексаторов, плохо подходят для объединенных индексов. Обычно вам нужны содержательные и уникальные значения ключей, которые уже существуют в источниках данных и (или) легко могут быть добавлены к ним.

## <a name="4---explore-the-code"></a>4\. Обзор кода

Когда вы завершите отправку данных и настройку параметров конфигурации, пример программы **AzureSearchMultipleDataSources.sln** будет готов к сборке и запуску.

Это простое консольное приложение для C#/.NET выполняет следующие задачи:

* создает индекс на основе структуры данных в классе C# Hotel (который также ссылается на классы Address и Room);
* создает источник данных и индексатор, который сопоставляет данные в Azure Cosmos DB с полями индекса (это объекты в службе "Когнитивный поиск Azure");
* запускает индексатор для загрузки данных Hotel из Cosmos DB;
* создает второй источник данных и индексатор, который сопоставляет поля индекса и данные больших двоичных объектов в формате JSON;
* запускает второй индексатор для загрузки данных Rooms из хранилища BLOB-объектов.

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

Большие двоичные объекты в формате JSON содержат поля ключа с именем **`Id`** вместо **`HotelId`** . В коде используется класс `FieldMapping`, сообщающий индексатору о необходимости направить значения поля **`Id`** в ключ документа **`HotelId`** в индексе.

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

  ![Список индексов службы Когнитивный поиск Azure](media/tutorial-multiple-data-sources/index-list.png "Список индексов службы Когнитивный поиск Azure")

Щелкните индекс hotel-rooms-sample в этом списке. Вы увидите интерфейс обозревателя поиска для этого индекса. Введите запрос для поиска термина, например Luxury. Вы увидите результат поиска с хотя бы одним документом, в котором отображается список объектов номеров из массива rooms.

## <a name="reset-and-rerun"></a>Сброс и повторный запуск

На ранних экспериментальных этапах разработки самый практичный подход к итерации схемы — удалить все объекты из службы "Когнитивный поиск Azure" и восстановить их с помощью кода. Имена ресурсов являются уникальными. Удаление объекта позволяет воссоздать его с использованием того же имени.

Пример кода для этого учебника проверяет имеющиеся объекты и удаляет их, чтобы вы могли повторно выполнить код.

Для удаления индексов, индексаторов и источников данных вы также можете использовать портал.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы работаете в своей подписке, после завершения проекта целесообразно удалить созданные ресурсы, которые вам больше не потребуются. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Просматривать ресурсы и управлять ими можно на портале с помощью ссылок "Все ресурсы" или "Группы ресурсов" в области навигации слева.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы ознакомились с процессом приема данных из нескольких источников, давайте подробнее рассмотрим конфигурацию индексатора, начав с Cosmos DB.

> [!div class="nextstepaction"]
> [Индексирование данных Cosmos DB с помощью индексатора в службе "Когнитивный поиск Azure"](search-howto-index-cosmosdb.md)