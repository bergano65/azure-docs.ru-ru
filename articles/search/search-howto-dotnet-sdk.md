---
title: Используйте azure Cognitive Search в .NET
titleSuffix: Azure Cognitive Search
description: Узнайте, как использовать Azure Cognitive Search в приложении .NET с помощью C и .NET SDK. Задачи на основе кода включают подключение к службе, индексирование содержимого и запрос индекса.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283073"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Как использовать Azure Cognitive Search из приложения .NET

Эта статья представляет собой пошаговый шаг, чтобы вы работаете с [Azure когнитивного поиска .NET SDK](https://aka.ms/search-sdk). Вы можете использовать .NET SDK для реализации богатого поиска в приложении с помощью Azure Cognitive Search.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Что в SDK когнитивного поиска Azure
Пакет SDK содержит клиентские библиотеки, позволяющие управлять индексами, источниками данных, индексаторами и картами синонимов, а также отправлять документы, управлять ими и выполнять запросы, не вникая в детали HTTP и JSON. Эти клиентские библиотеки распределяются как пакеты NuGet.

Главный пакет NuGet — `Microsoft.Azure.Search`. Он представляет собой мета пакет, содержащий все остальные пакеты в виде зависимостей. Используйте этот пакет, если вы только начинаете работу или если вы знаете, что вашему приложению понадобятся все функции когнитивного поиска Azure.

Ниже перечислены другие пакеты NuGet в пакете SDK.
 
  - `Microsoft.Azure.Search.Data`: Используйте этот пакет, если вы разрабатываете приложение .NET с помощью Azure Cognitive Search, и вам нужно только задать запрос или обновить документы в индексах. Если требуется также создавать или обновлять индексы, карты синонимов и другие ресурсы уровня службы, используйте пакет `Microsoft.Azure.Search`.
  - `Microsoft.Azure.Search.Service`— Используйте этот пакет, если вы разрабатываете автоматизацию в .NET для управления индексами когнитивного поиска Azure, синонимными картами, индексаторами, источниками данных или другими ресурсами уровня обслуживания. Если требуется только запрашивать и обновлять документы в индексах, используйте пакет `Microsoft.Azure.Search.Data`. Если вам нужна вся функциональность Azure `Microsoft.Azure.Search` Cognitive Search, используйте пакет вместо этого.
  - `Microsoft.Azure.Search.Common`: Общие типы, необходимые библиотекам Azure Cognitive Search .NET. Вам не нужно использовать этот пакет непосредственно в приложении. Он предназначен только для использования в качестве зависимости.

Различные клиентские библиотеки определяют такие классы, как `Index`, `Field` и `Document`, а также такие операции, как `Indexes.Create` и `Documents.Search` в классах `SearchServiceClient` и `SearchIndexClient`. Эти классы упорядочены в следующие подразделы:

* [Microsoft.Azure.Поиск](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Если вы хотите предоставить обратную связь для будущего обновления SDK, просмотрите нашу [страницу отзывов](https://feedback.azure.com/forums/263029-azure-search/) или создайте проблему на [GitHub](https://github.com/azure/azure-sdk-for-net/issues) и упомяните в заголовке вопроса «Azure Cognitive Search».

SDK .NET поддерживает `2019-05-06` версию [API Azure Cognitive Search REST.](https://docs.microsoft.com/rest/api/searchservice/) Эта версия включает в себя поддержку [сложных типов,](search-howto-complex-data-types.md) [обогащения ИИ,](cognitive-search-concept-intro.md) [автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete)и [разогнания JsonLines](search-howto-index-json-blobs.md) при индексации Azure Blobs. 

Этот пакет SDK не поддерживает такие [операции управления](https://docs.microsoft.com/rest/api/searchmanagement/), как создание и масштабирование служб поиска или управление ключами API. Если вам необходимо управлять ресурсами поиска из приложения .NET, вы можете использовать [Azure Cognitive Search .NET Management SDK.](https://aka.ms/search-mgmt-sdk)

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Обновление до последней версии пакета SDK
Если вы уже используете старую версию Azure Cognitive Search .NET SDK и хотите перейти на последнюю общедоступную версию, [в этой статье объясняется,](search-dotnet-sdk-migration-version-9.md) как это сделать.

## <a name="requirements-for-the-sdk"></a>Требования для пакета SDK
1. Визуальная студия 2017 или более поздней.
2. Ваш собственный сервис когнитивного поиска Azure. Чтобы использовать пакет SDK, необходимо указать имя службы и один или несколько ключей API. [Создание службы на портале](search-create-service-portal.md) поможет вам это сделать.
3. Загрузите пакет Azure Cognitive Search .NET SDK [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) с помощью пакета «Управление NuGet» в Visual Studio. Достаточно найти имя пакета `Microsoft.Azure.Search` на сайте NuGet.org (или одно из указанных выше имен пакета, если требуется только подмножество функциональных возможностей).

Azure Cognitive Search .NET SDK поддерживает приложения, ориентированные на .NET Framework 4.5.2 и выше, а также .NET Core 2.0 и выше.

## <a name="core-scenarios"></a>Основные сценарии
В приложении поиска необходимо выполнить несколько действий. В данном руководстве мы обсудим эти основные сценарии:

* Создание индекса
* Заполнение индекса документами
* Поиск документов с помощью полнотекстового поиска и фильтров

Следующий пример кода иллюстрирует каждый из этих сценариев. Вы можете использовать фрагменты кода в собственном приложении.

### <a name="overview"></a>Обзор
Образец приложения, который мы будем рассматривать, создает новый индекс с именем hotels, заполняет его несколькими документами, а затем выполняет некоторые поисковые запросы. Вот основная программа, показывающая общий поток.

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Полный исходный код образца приложения, используемого в этом пошаговом руководстве, см. в репозитории [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Мы рассмотрим ее шаг за шагом. Для начала необходимо создать `SearchServiceClient`. Этот объект позволяет управлять индексами. Для того, чтобы создать его, необходимо указать имя службы когнитивного поиска Azure, а также ключ API-на-адиона. Эту информацию можно указать в файле `appsettings.json` из [примера приложения](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Если указать неправильный ключ (например, ключ запроса вместо ключа администратора), `SearchServiceClient` вызовет `CloudException` с сообщением об ошибке "Доступ запрещен" при первом вызове метода операции, например `Indexes.Create`. В этом случае проверьте ключ API еще раз.
> 
> 

Следующие несколько строк вызывают методы для создания индекса под названием hotels, предварительно удалив его, если он уже существует. Мы рассмотрим эти методы немного позже.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Затем необходимо заполнить индекс. Чтобы заселить индекс, нам `SearchIndexClient`понадобится . Его можно получить двумя способами: создать его самостоятельно или вызвать метод `Indexes.GetClient` в `SearchServiceClient`. Для удобства мы используем второй способ.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> В типичном приложении поиска управление индексами и численность населения могут обрабатываться отдельным компонентом из поисковых запросов. `Indexes.GetClient`удобна для заполнения индекса, потому что это `SearchCredentials`экономит вам проблемы с предоставлением дополнительных. Это достигается благодаря передачи ключа администратора, который использовался для создания `SearchServiceClient`, в новый элемент `SearchIndexClient`. Однако в той части приложения, которая выполняет запросы, `SearchIndexClient` лучше создать непосредственно, чтобы вы могли пройти в ключе запроса, который позволяет только читать данные, а не ключ админ. Это согласуется с принципом наименьших прав доступа и поможет сделать приложение более безопасным. Дополнительные сведения о ключах администраторов и запросов см. [здесь](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Теперь, когда у нас есть `SearchIndexClient`, мы можем заполнить индекс. Индекс населения осуществляется другим методом, который мы будем ходить позже.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Наконец, мы выполним несколько поисковых запросов и отобразим результаты. На этот раз мы используем другой метод `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Метод `RunQueries` мы позже рассмотрим подробно. Это код для создания нового объекта `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

На этот раз мы воспользуемся ключом запроса, так как доступ на запись к индексу не требуется. Эту информацию можно указать в файле `appsettings.json` из [примера приложения](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Если вы запустите это приложение с действительным именем службы и клавишами API, выход должен выглядеть следующим образом: (Некоторые выходные консоли были заменены на "..." для иллюстраций.)

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

Полный исходный код приложения представлен в конце этой статьи.

Далее мы подробнее рассмотрим каждый из методов, вызываемых элементом `Main`.

### <a name="creating-an-index"></a>Создание индекса
После создания `SearchServiceClient` `Main` индекса «отели», если он уже существует. Это удаление осуществляется следующим методом:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Этот метод использует соответствующий `SearchServiceClient` , чтобы проверить наличие индекса, и если он существует, удалить его.

> [!NOTE]
> Пример кода в этой статье использует синхронные методы Azure Cognitive Search .NET SDK для простоты. Рекомендуем использовать в ваших приложениях асинхронные методы, чтобы сохранить масштабируемость и скорость отклика. Например, в приведенном выше методе можно использовать `ExistsAsync` и `DeleteAsync` вместо `Exists` и `Delete`.
> 
> 

Затем `Main` создает новый индекс hotels, вызывая следующий метод:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Этот метод создает новый объект `Index` со списком объектов `Field`, который определяет схему нового индекса. Каждое поле имеет имя, тип данных и несколько атрибутов, которые определяют его поведение при поиске. Класс `FieldBuilder` использует отражение, чтобы создать список объектов `Field` для соответствующего индекса. Он проверяет общедоступные свойства и атрибуты полученного класса модели `Hotel`. Класс `Hotel` мы рассмотрим подробно позже.

> [!NOTE]
> При необходимости вы всегда можете создать список объектов `Field` напрямую, а не с помощью `FieldBuilder`. Это полезно, например, если вы не хотите использовать класс модели или используете существующий класс модели, в который не хотите добавлять атрибуты.
>
> 

В дополнение к полям, вы также можете добавить скоринг профилей, предлагатели, или CORS параметры индекса (эти параметры опущены из образца для краткости). Более подробную информацию об объекте Index и его составных частях можно найти в [справке SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)а также в [справке Azure Cognitive Search REST API.](https://docs.microsoft.com/rest/api/searchservice/)

### <a name="populating-the-index"></a>Заполнение индекса
Следующим шагом `Main` в заполнении вновь созданного индекса. Данный индекс населения выполняется следующим методом: (Некоторые коды заменены на "..." для иллюстраций.  Ознакомьтесь с полным выборчным решением для полного кода популяции данных.)

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Этот метод состоит из четырех частей. Первый создает массив `Hotel` из 3 `Room` объектов каждый из 3 объектов, которые будут служить в качестве наших входных данных для загрузки в индекс. Эти данные жестко запрограммированы для простоты. В вашем приложении данные скорее всего будут поступать из внешнего источника, например базы данных SQL.

Вторая часть создает `IndexBatch` , содержащий документы. Вы указываете операцию, которую необходимо применить к пакету во время его создания, в данном случае путем вызова метода `IndexBatch.Upload`. Затем пакет загружается методом `Documents.Index` в индекс когнитивного поиска Azure.

> [!NOTE]
> В этом примере мы просто отправляем документы. Если требуется внести изменения в существующие документы или удалить документы, можно создать пакеты, вызвав методы `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` или `IndexBatch.Delete`. Вы также можете смешивать различные `IndexBatch.New`операции в одной `IndexAction` партии, вызывая, которая занимает коллекцию объектов, каждая из которых говорит Azure Cognitive Search для выполнения определенной операции на документе. Можно создать каждый `IndexAction` с отдельной операцией, вызвав соответствующий метод, например `IndexAction.Merge`, `IndexAction.Upload` и т. д.
> 
> 

Третья часть метода — это блок catch, который обрабатывает важные ошибки индексирования. Если служба когнитивного поиска Azure не индексирует некоторые `IndexBatchException` документы `Documents.Index`в пакете, Это исключение может произойти, если вы индексируете документы, пока ваша служба находится под большой нагрузкой. **Настоятельно рекомендуется явно обрабатывать этот случай в коде.**  Вы можете задержать и повторить попытку индексирования соответствующих документов либо занести ошибку в журнал и продолжить работу, как в нашем примере, а также выполнить другие действия в зависимости от требований вашего приложения к целостности данных.

> [!NOTE]
> Метод можно [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) использовать для построения новой партии, содержащей `Index`только действия, которые не удались при предыдущем вызове. На сайте [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry) опубликовано обсуждение правил его использования.
>
>

И наконец, метод `UploadDocuments` вызывает задержку на две секунды. Индексирование происходит асинхронно в службе когнитивного поиска Azure, поэтому примерприложения нужно подождать некоторое время, чтобы убедиться, что документы доступны для поиска. Такие задержки обычно необходимы только в демонстрациях, тестах и примерах приложений.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Обработка документов пакетом SDK для .NET
Вы можете быть удивлены, как Azure Cognitive Search .NET SDK может `Hotel` загружать экземпляры класса, определенного пользователем, как в индекс. Чтобы ответить на этот вопрос, рассмотрим класс `Hotel` :

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

Первое, что следует заметить, что имя `Hotel` каждого государственного имущества в классе будет отображаться на поле с тем же именем в определении индекса. Если вы хотите, чтобы каждое поле начиналось с буквы нижнего регистра ("дело верблюда"), вы можете сказать `[SerializePropertyNamesAsCamelCase]` SDK сопоставить имена свойств с верблюжьим корпусом автоматически с атрибутом в классе. Этот сценарий распространен в приложениях .NET, которые выполняют связывание данных, когда целевая схема находится вне контроля разработчика приложения без нарушения руководящих принципов именования "паскаль" в .NET.

> [!NOTE]
> Azure Cognitive Search .NET SDK использует библиотеку [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) для сериализации и десеризации пользовательских объектов модели и от JSON. При необходимости эту сериализацию можно настроить. Для получения дополнительной информации см [JSON.NET.](#JsonDotNet)
> 
> 

Вторая вещь, чтобы заметить, каждое `IsFilterable`свойство украшено атрибутами, такими как , `IsSearchable`, `Key`и `Analyzer`. Эти атрибуты отображаются непосредственно к [соответствующим атрибутам поля в индексе когнитивного поиска Azure.](/rest/api/searchservice/create-index) Класс `FieldBuilder` использует эти свойства для построения определений полей для индекса.

Третья важная вещь `Hotel` в классе — это типы данных публичных свойств. Типы .NET этих свойств сопоставляются с эквивалентными типами полей в определении индекса. Например, свойство строки `Category` сопоставляется с полем `category`, которое имеет тип `Edm.String`. Есть аналогичные типы `bool?`карт `Edm.Boolean` `DateTimeOffset?`между `Edm.DateTimeOffset` , , и и так далее. Конкретные правила для отображения `Documents.Get` типов задокументированы методом в [ссылке Azure Cognitive Search .NET SDK.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get) Хотя класс `FieldBuilder` выполняет это сопоставление, этот механизм полезно понимать на случай, если вам понадобится устранять неполадки, связанные с сериализацией.

Вы случайно не `SmokingAllowed` заметили собственности?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

Атрибут `JsonIgnore` на этом свойстве `FieldBuilder` говорит не сериализировать его к индексу как поле.  Это отличный способ создать вычисленное свойств стороны клиента, которые можно использовать в качестве помощников в приложении.  В этом случае, `SmokingAllowed` свойство `Room` отражает `Rooms` ли любой в коллекции позволяет курить.  Если все они ложные, это означает, что весь отель не позволяет курить.

Некоторые свойства, `Address` `Rooms` такие как и экземпляры классов .NET.  Эти свойства представляют собой более сложные структуры данных и, как следствие, требуют полей со [сложным типом данных](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) в индексе.

Свойство `Address` представляет собой набор из `Address` нескольких значений в классе, определяемых ниже:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

Этот класс содержит стандартные значения, используемые для описания адресов в США или Канаде. Такие типы можно использовать для сгруппирования логических полей в индексе.

Свойство `Rooms` представляет массив `Room` объектов:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

Ваша модель данных в .NET и соответствующая схема индекса должны быть разработаны для поддержки поиска, который вы хотели бы предоставить конечному пользователю. Каждый объект верхнего уровня в .NET, т.е. документ в индексе, соответствует результату поиска, который вы бы представилв в пользовательском интерфейсе. Например, в приложении поиска отеля конечные пользователи могут захотеть искать по названию отеля, особенностям отеля или характеристикам конкретного номера. Мы рассмотрим некоторые примеры запросов немного позже.

Эта способность использовать собственные классы для взаимодействия с документами в индексе работает в обоих направлениях; Вы также можете получить результаты поиска и поиметь SDK автоматически десериализировать их на тип по вашему выбору, как мы увидим в следующем разделе.

> [!NOTE]
> Azure Cognitive Search .NET SDK также поддерживает динамически `Document` набранные документы с помощью класса, который является картированием ключей/значений полей для значений полей. Это полезно в тех случаях, когда во время разработки вам неизвестна схема индекса или привязка к конкретным классам моделей нецелесообразна. Все методы пакета SDK, связанные с документами, имеют перегрузки, работающие с классом `Document`, а также строго типизированные перегрузки, принимающие параметр универсального типа. Пример кода в данном руководстве содержит только перегрузки второго типа. [ `Document` Класс](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) наследует `Dictionary<string, object>`от .
> 
>

**Почему следует использовать типы данных, допускающие значение NULL**

При проектировании собственных классов моделей для отображения в индекс azure `bool` `int` Cognitive Search мы рекомендуем `bool?` декларировать свойства типов значений, таких как и быть недействительными (например, `bool`вместо). При использовании ненулевого свойства необходимо **гарантировать** , что документы в индексе не будут содержать значение NULL для соответствующего поля. Ни SDK, ни служба когнитивного поиска Azure не помогут вам обеспечить выполнение этой функции.

Это не просто гипотетическое соображение. Представьте себе ситуацию, когда вы добавляете новое поле в существующий индекс с типом `Edm.Int32`. После обновления определения индекса все документы будут иметь нулевую величину для этого нового поля (поскольку все типы являются недействительными в Azure Cognitive Search). Если затем для этого поля вы используете класс модели со свойством `int`, не допускающим нулевое значение, при попытке получения документов вы получите `JsonSerializationException` следующим образом:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

По этой причине по-прежнему рекомендуется использовать типы, допускающие значения NULL, в классах модели.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Пользовательская сериализация с помощью JSON.NET
Пакет SDK использует JSON.NET для сериализации и десериализации документов. Вы можете настроить сериализацию и десериализацию, если это необходимо, определив свой собственный `JsonConverter` или `IContractResolver`. Для получения дополнительной информации смотрите [JSON.NET документацию](https://www.newtonsoft.com/json/help/html/Introduction.htm). Это может быть полезно, если требуется адаптировать существующий класс моделей из приложения для использования в Azure Cognitive Search и других более продвинутых сценариях. Например, с помощью пользовательской сериализации можно делать следующее.

* Включить или исключить определенные свойства класса модели с сохранением в виде поля документа.
* Реализовать сопоставление между именами свойств в коде и именами полей в индексе.
* Создать настраиваемые атрибуты, которые можно использовать для сопоставления свойств с полями документов.

Вы можете найти примеры реализации пользовательской сериализации в модульных тестах для Azure Cognitive Search .NET SDK на GitHub. Хорошей отправной точкой будет [эта папка](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Она содержит классы, используемые в тестах пользовательской сериализации.

### <a name="searching-for-documents-in-the-index"></a>Поиск документов в индексе
Последним шагом в примере приложения является поиск некоторых документов в индексе:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

При каждом выполнении запроса этот метод прежде всего создает объект `SearchParameters`. Этот объект используется для указания дополнительных параметров для запроса, таких как сортировка, фильтрация, paging и гране. В этом методе мы задаем свойства `Filter`, `Select`, `OrderBy` и `Top` для разных запросов. Все свойства `SearchParameters` описаны [здесь](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Следующий этап — фактическое выполнение поискового запроса. Запуск поиска выполняется `Documents.Search` с помощью метода. Для каждого запроса мы передаем текст для поиска в строковом формате (или `"*"`, если текст для поиска отсутствует) и созданные ранее параметры поиска. Мы также указываем `Hotel` как параметр типа `Documents.Search`, в результате чего пакет SDK будет десериализовать документы в результатах поиска к объектам типа `Hotel`.

> [!NOTE]
> Дополнительные сведения о синтаксисе выражений запросов см. [здесь](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

А теперь, после завершения каждого запроса (поиска совпадений в результатах поиска), этот метод последовательно выводит в консоль все удовлетворяющие условиям документы.

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Давайте подробнее рассмотрим каждый из запросов. Ниже приведен код для выполнения первого запроса:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

В этом случае мы ищем весь индекс слова «мотель» в любом поле поиска и хотим получить только `Select` названия отелей, как указано в параметре. Результаты приведены ниже.

    Name: Secret Point Motel

    Name: Twin Dome Motel

Следующий запрос немного интереснее.  Мы хотим найти любые отели, в которых есть номер с ночным тарифом менее $100 и вернуть только идентификатор отеля и описание:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Этот запрос использует выражение OData `$filter` (по значению `Rooms/any(r: r/BaseRate lt 100)`), чтобы отфильтровать в индексе нужные документы. При этом [любой оператор](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) применяет «BaseRate lt 100» к каждому элементу коллекции Rooms. Вы можете узнать больше о синтаксисе OData, который поддерживает Azure Cognitive Search [здесь.](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)

Вот результат выполнения этого запроса:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Теперь нам нужно найти две гостиницы с самым свежим ремонтом и отобразить для них название и дату ремонта. Ниже приведен код: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

В этом случае мы снова используем синтаксис OData, присвоив параметру `OrderBy` значение `lastRenovationDate desc`. Мы также указываем значение 2 для параметра `Top`, чтобы получить только два первых документа. Как и ранее, параметр `Select` содержит поля, которые мы хотим получить.

Результаты приведены ниже.

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Наконец, мы хотим найти все названия отелей, которые соответствуют слову "отель":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

Ниже представлены результаты этого запроса. Они включают все поля, так как мы не указали свойство `Select`:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

На этом руководство заканчивается, но вам не стоит останавливаться. Следующие шаги предоставляют дополнительные ресурсы для получения дополнительной информации о когнитивном поиске Azure.

## <a name="next-steps"></a>Дальнейшие действия
* Изучите справочную информацию о [пакете SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) и [REST API](https://docs.microsoft.com/rest/api/searchservice/).
* Изучите [соглашения о наименовании](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) , чтобы узнать правила именования различных объектов.
* Обзор [поддерживает типы данных](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) в Azure Cognitive Search.
