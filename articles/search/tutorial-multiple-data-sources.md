---
title: Руководство по C#. Индексирование нескольких источников данных (Поиск Azure)
description: Узнайте, как импортировать данные из нескольких источников данных в один индекс Поиска Azure.
author: RobDixon22
manager: nitinme
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: heidist
ms.openlocfilehash: d55a586d3dfb22b5dad377ff656b8d6a6c940bdb
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241838"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>Руководство по C#. Объединение данных из нескольких источников данных в один индекс Поиска Azure

Поиск Azure умеет импортировать, анализировать и индексировать данные из нескольких источников данных в объединенный индекс для поиска. Это нужно для тех ситуаций, когда структурированные данные объединяются с менее структурированными данными или даже простым текстом из других источников, таких как текстовые документы, код HTML и JSON.

Этот учебник описывает, как индексировать данные об отелях из источника данных Azure Cosmos DB и как объединить их с подробными сведениями о номерах отелей, извлеченными из документов в хранилище BLOB-объектов. В результате мы получим индекс поиска данных об отелях со сложными типами данных.

В этом учебнике с помощью C#, пакета SDK .NET для Поиска Azure и портала Azure вы выполним следующие задачи:

> [!div class="checklist"]
> * отправка примера данных и создание источников данных;
> * определение ключа документа;
> * определение и создание индекса;
> * индексирование данных об отелях из Azure Cosmos DB;
> * объединение данных о номерах отелей из хранилища BLOB-объектов.

## <a name="prerequisites"></a>Предварительные требования

В этом кратком руководстве используются приведенные ниже службы, инструменты и данные. 

- [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом учебнике.

- [Создайте учетную запись Azure Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) для хранения примера данных об отелях.

- [Создайте учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) для хранения примера данных в формате больших двоичных объектов JSON.

- [Установите Visual Studio](https://visualstudio.microsoft.com/), чтобы использовать решение в качестве интегрированной среды разработки.

### <a name="install-the-project-from-github"></a>Установка проекта из GitHub

1. Найдите на сайте GitHub репозиторий с примерами [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Выберите действие **Клонировать или скачать**, чтобы создать частную локальную копию этого репозитория.
1. Откройте Visual Studio и установите пакет NuGet для Поиска Microsoft Azure, если он еще не установлен. В меню **Средства** выберите **Диспетчер пакетов NuGet**, а затем **Manage NuGet Packages for Solution...** (Управление пакетами NuGet для решения...). На вкладке **Обзор** введите в поле поиска "Azure Search" (Поиск Azure). В результатах поиска выберите **Microsoft.Azure.Search** (версии 9.0.1 или более поздней) и установите эту службу. Для завершения установки нужно пройти серию дополнительных диалоговых окон.

    ![Добавление библиотек Azure с помощью NuGet](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. С помощью Visual Studio перейдите в локальный репозиторий и откройте файл решения **AzureSearchMultipleDataSources.sln**.

## <a name="get-a-key-and-url"></a>Получение ключа и URL-адреса

Для взаимодействия со службой "Поиск Azure" вам нужны URL-адрес службы и ключ доступа. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

![Получение конечной точки HTTP и ключа доступа](media/search-get-started-postman/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Для выполнения любого запроса к службе требуется использование ключа API. Действительный ключ устанавливает для каждого запроса отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Подготовка примера данных Azure Cosmos DB

В этом примере используются два небольших набора данных, которые описывают семь вымышленных отелей. Один набор, который описывает сами отели, мы загрузим в базу данных Azure Cosmos DB. Другой набор с подробными сведениями о номерах в этих отелях предоставляется в виде семи отдельных файлов формата JSON, и его мы отправим в хранилище BLOB-объектов Azure.

1. [Войдите на портал Azure](https://portal.azure.com) и перейдите к странице общих сведений об учетной записи Azure Cosmos DB.

1. Щелкните "Добавить контейнер" в строке меню. Выберите "Создать новую базу данных" и укажите для нее имя **hotel-rooms-db**. В качестве имени коллекции введите **hotels**, а имени ключа — **/HotelId**. Щелкните **ОК**, чтобы создать базу данных и контейнер.

   ![Добавление контейнера Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-add-container.png "Add an Azure Cosmos DB container")

1. Перейдите в обозреватель данных Cosmos DB и выберите элемент **items** в контейнере **hotels** из базы данных **hotel-rooms-db**. На панели команд нажмите **Отправить элемент**.

   ![Отправка данных в коллекцию Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Upload to Cosmos DB collection")

1. На панели отправки щелкните кнопку со значком папки, а затем перейдите к файлу **cosmosdb/HotelsDataSubset_CosmosDb.json** в папке проекта. Щелкните **ОК**, чтобы начать отправку.

   ![Выбор файла для отправки](media/tutorial-multiple-data-sources/cosmos-upload2.png "Select file to upload")

1. С помощью кнопки "Обновить" освежите представление элементов в коллекции hotels. Вы увидите в списке семь новых документов базы данных.

## <a name="prepare-sample-blob-data"></a>Подготовка большого двоичного объекта с примером данных

1. [Войдите на портал Azure](https://portal.azure.com), перейдите к учетной записи хранения Azure, выберите **Большие двоичные объекты**, а затем щелкните **Контейнер**.

1. [Создайте контейнер больших двоичных объектов](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) с именем **hotel-rooms**, в котором будут храниться JSON-файлы с примерами данных о номерах отелей. Можно задать любое из допустимых значений уровня общего доступа.

   ![Создание контейнера больших двоичных объектов](media/tutorial-multiple-data-sources/blob-add-container.png "Create a blob container")

1. Откройте контейнер после создания и на панели команд выберите **Загрузить**.

   ![Кнопка "Загрузить" на панели команд](media/search-semi-structured-data/upload-command-bar.png "Upload on command bar")

1. Перейдите к папке, содержащей примеры файлов. Выберите их, а затем щелкните **Загрузить**.

   ![Загрузка файлов](media/tutorial-multiple-data-sources/blob-upload.png "Upload files")

Когда отправка файлов завершится, они появятся в списке внутри контейнера данных.

## <a name="set-up-connections"></a>Настройка подключений

Сведения о подключении к службе поиска и к источникам данных определяются в файле решения **appsettings.json**. 

1. В Visual Studio откройте файл **AzureSearchMultipleDataSources.sln**.

1. В обозревателе решений измените файл **appsettings.json**.  

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

Первые две записи содержат URL-адрес и ключи администратора для службы "Поиск Azure". Например, если конечная точка имеет имя`https://mydemo.search.windows.net`, следует указать имя службы `mydemo`.

Следующие записи содержат имена учетных записей и строки подключения для хранилища BLOB-объектов Azure и источников данных Azure Cosmos DB.

### <a name="identify-the-document-key"></a>Определение ключа документа

В Поиске Azure ключ поля однозначно определяет каждый документ в индексе. Каждый индекс поиска должен содержать только одно поле ключа типа `Edm.String`. Это поле ключа должно присутствовать в источнике данных для каждого документа, который добавляется к индексу. (Фактически, это единственное обязательное для заполнения поле.)

При индексировании данных из нескольких источников данных необходимо сопоставить каждый источник данных с одним ключевым полем в составном индексе. Зачастую придется потратить некоторое время, чтобы заранее составить разумный ключ документов для создаваемого индекса и убедиться, что он существует в каждом источнике данных.

Индексаторы Поиска Azure умеют использовать сопоставления полей для изменения имен и форматов полей данных в процессе индексирования, чтобы правильно сопоставить данные из источника с нужным полем индекса.

Например, в нашем примере данных Azure Cosmos DB идентификатор отеля имеет имя **HotelId**. Но в большом двоичном объекте с информацией о номерах отелей, который хранится в виде JSON-файлов, этот идентификатор именуется **Id**. Программа решает эту проблему, сопоставляя поле **Id** из больших двоичных объектов c ключевым полем **HotelId** в индексе.

> [!NOTE]
> В большинстве случаев автоматически сформированные ключи документов, например стандартные ключи некоторых индексаторов, плохо подходят для объединенных индексов. Обычно вам нужны содержательные и уникальные значения ключей, которые уже существуют в источниках данных и (или) легко могут быть добавлены к ним.

## <a name="understand-the-code"></a>Изучение кода

Когда вы завершите отправку данных и настройку параметров конфигурации, пример программы **AzureSearchMultipleDataSources.sln** будет готов к сборке и запуску.

Это простое консольное приложение для C#/.NET выполняет следующие задачи:
* создает новый индекс Поиска Azure на основе структуры данных в классе C# Hotel (который также ссылается на классы Address и Room);
* создает источник данных и индексатор Azure Cosmos DB, которые сопоставляют индексные поля и данные в Azure Cosmos DB;
* запускает индексатор Azure Cosmos DB для загрузки данных Hotel;
* создает источник данных и индексатор в хранилище BLOB-объектов Azure, которые сопоставляют индексные поля и данные в больших двоичных объектов JSON;
* выполняет индексатор хранилища BLOB-объектов Azure для загрузки данных Rooms.

 Перед выполнением этой программы вам стоит изучить ее код и определения индекса и индексатора, настроенные для нашего примера. Соответствующий код находится в двух файлах:

  + **Hotel.cs** содержит схему, которая определяет индекс;
  + **Program.cs** содержит функции, которые создают индекс Поиска Azure, источники данных и индексаторы, а также отправляют в индекс объединенные результаты.

### <a name="define-the-index"></a>Определение индекса

Этот пример программы использует пакет SDK для .NET, чтобы определить и создать индекс Поиска Azure. С помощью [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) из класса C# модели данных программа создает структуру индекса.

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

Большие двоичные объекты в формате JSON содержат ключевое поле с именем **Id** вместо **HotelId**. В коде настраивается класс `FieldMapping`, сообщающий индексатору о необходимости направить значения поля **Id** в ключевое поле **HotelId** в индексе.

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

## <a name="search-your-json-files"></a>Поиск JSON-файлов

Вы можете изучить заполненный индекс поиска на портале после выполнения программы с помощью компонента [**Обозреватель поиска**](search-explorer.md).

На портале Azure откройте страницу **Обзор** для службы поиска и найдите в списке **Индексы** созданный индекс **hotel-rooms-sample**.

  ![Список индексов Поиска Azure](media/tutorial-multiple-data-sources/index-list.png "List of Azure Search indexes")

Щелкните индекс hotel-rooms-sample в этом списке. Вы увидите интерфейс обозревателя поиска для этого индекса. Введите запрос для поиска термина, например Luxury. Вы увидите результат поиска с хотя бы одним документом, в котором отображается список объектов номеров из массива rooms.

## <a name="clean-up-resources"></a>Очистка ресурсов

Самый быстрый способ очистки по завершении работы с руководством — удалить группу ресурсов, содержащую службу "Поиск Azure". Теперь можно удалить группу ресурсов вместе со всем ее содержимым без возможности восстановления. На портале имя группы ресурсов находится на странице "Обзор" службы "Поиск Azure".

## <a name="next-steps"></a>Дополнительная информация

Существует ряд подходов и несколько вариантов индексирования больших двоичных объектов JSON. Если источник данных включает в себя содержимое JSON, вы можете изучить разные варианты и выбрать наиболее подходящий для вашего сценария.

> [!div class="nextstepaction"]
> [Как индексировать большие двоичные объекты JSON с помощью индексатора больших двоичных объектов Поиска Azure](search-howto-index-json-blobs.md)

Может потребоваться дополнить структурированные данные индекса, полученные из одного источника данных, интеллектуально обогащенными данными из неструктурированных BLOB-объектов или содержимым для полнотекстового поиска. Следующие руководства демонстрируют, как применить Cognitive Services совместно с Поиском Azure, используя пакет SDK для .NET.

> [!div class="nextstepaction"]
> [Вызов API-интерфейсов Cognitive Services в конвейере индексирования службы "Поиск Azure"](cognitive-search-tutorial-blob-dotnet.md)
