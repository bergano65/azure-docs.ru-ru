---
title: Использование службы "Поиск Azure" в приложении .NET — Поиск Azure
description: Узнайте, как использовать Поиск Azure в приложении .NET с помощью C# и пакет SDK для .NET. Задачи на основе кода включают подключение к службе, индексирование содержимого и запрос индекса.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: brjohnst
ms.openlocfilehash: 9f0af40d442747181636b50612f7d2162ead6a86
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450018"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Использование службы поиска Azure в приложении .NET

В этой статье представлено пошаговое руководство, которое поможет вам приступить к работе с [SDK для Поиска Azure в .NET](https://aka.ms/search-sdk). Пакет SDK для .NET позволяет реализовать широкие возможности поиска в приложении с помощью службы поиска Azure.

## <a name="whats-in-the-azure-search-sdk"></a>Из чего состоит SDK для Поиска Azure
Пакет SDK содержит клиентские библиотеки, позволяющие управлять индексами, источниками данных, индексаторами и картами синонимов, а также отправлять документы, управлять ими и выполнять запросы, не вникая в детали HTTP и JSON. Эти клиентские библиотеки распределяются как пакеты NuGet.

Главный пакет NuGet — `Microsoft.Azure.Search`. Он представляет собой мета пакет, содержащий все остальные пакеты в виде зависимостей. Используйте этот пакет, если вы только начинаете свою работу с Azure или знаете, что вашему приложению понадобятся все функции службы поиска Azure.

Ниже перечислены другие пакеты NuGet в пакете SDK.
 
  - `Microsoft.Azure.Search.Data`: используйте этот пакет, если вы разрабатываете приложение .NET с помощью службы "Поиск Azure" и вам необходимо только запрашивать и обновлять документы в индексах. Если требуется также создавать или обновлять индексы, карты синонимов и другие ресурсы уровня службы, используйте пакет `Microsoft.Azure.Search`.
  - `Microsoft.Azure.Search.Service`: используйте этот пакет, если вы разрабатываете автоматизацию в .NET для управления индексами, картами синонимов, индексаторами, источниками данных службы "Поиск Azure" или другими ресурсами уровня службы. Если требуется только запрашивать и обновлять документы в индексах, используйте пакет `Microsoft.Azure.Search.Data`. Чтобы использовать все функциональные возможности службы поиска Azure, используйте пакет `Microsoft.Azure.Search`.
  - `Microsoft.Azure.Search.Common`: общие типы, необходимые библиотекам .NET службы "Поиск Azure". Необходимо использовать этот пакет непосредственно в приложении. Она предназначена только для использования в качестве зависимости.

Различные клиентские библиотеки определяют такие классы, как `Index`, `Field` и `Document`, а также такие операции, как `Indexes.Create` и `Documents.Search` в классах `SearchServiceClient` и `SearchIndexClient`. Эти классы упорядочены в следующие подразделы:

* [Microsoft.Azure.Search;](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Если вы хотите оставить отзыв для будущего обновления пакета SDK, см. в разделе наших [странице обратной связи](https://feedback.azure.com/forums/263029-azure-search/) или создать ошибку на [GitHub](https://github.com/azure/azure-sdk-for-net/issues) и упомянуть «Поиск Azure» в название проблемы.

Пакет SDK для .NET поддерживает версию `2019-05-06` [интерфейса REST API службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/). Эта версия включает поддержку [сложные типы](search-howto-complex-data-types.md), [когнитивный поиск](cognitive-search-concept-intro.md), [автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete), и [JsonLines режим анализа](search-howto-index-json-blobs.md) при Индексирование BLOB-объектов Azure. 

Этот пакет SDK не поддерживает такие [операции управления](https://docs.microsoft.com/rest/api/searchmanagement/), как создание и масштабирование служб поиска или управление ключами API. Если вы хотите управлять ресурсами службы поиска из приложения .NET, можно использовать [пакет SDK управления .NET для службы поиска Azure](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Обновление до последней версии пакета SDK
Если вы уже используете более старой версии пакета SDK для .NET для поиска Azure и вы бы хотели обновление до последней общедоступной версии [в этой статье](search-dotnet-sdk-migration-version-9.md) объясняется, каким образом.

## <a name="requirements-for-the-sdk"></a>Требования для пакета SDK
1. Visual Studio 2017 или более поздней версии.
2. Ваша личная служба поиска Azure. Чтобы использовать пакет SDK, необходимо указать имя службы и один или несколько ключей API. [Создание службы на портале](search-create-service-portal.md) поможет вам это сделать.
3. Скачайте пакет SDK для Поиска Azure Search в .NET в виде [пакета NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) с помощью команды "Управление пакетами NuGet" в Visual Studio. Достаточно найти имя пакета `Microsoft.Azure.Search` на сайте NuGet.org (или одно из указанных выше имен пакета, если требуется только подмножество функциональных возможностей).

Пакет SDK для .NET для поиска Azure поддерживает приложения, предназначенные для .NET Framework 4.5.2 и более поздних версий, как .NET Core 2.0 и более поздней версии.

## <a name="core-scenarios"></a>Основные сценарии
В приложении поиска необходимо выполнить несколько действий. В данном руководстве мы обсудим эти основные сценарии:

* Создание индекса
* Заполнение индекса документами
* Поиск документов с помощью полнотекстового поиска и фильтров

В следующем примере кода показано, каждый из этих сценариев. Вы можете использовать фрагменты кода в собственном приложении.

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

Мы рассмотрим ее шаг за шагом. Для начала необходимо создать `SearchServiceClient`. Этот объект позволяет управлять индексами. Чтобы создать его, необходимо указать имя службы поиска Azure и ключ API администратора. Эту информацию можно указать в файле `appsettings.json` из [примера приложения](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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

Затем необходимо заполнить индекс. Заполнение индекса, нам понадобится `SearchIndexClient`. Его можно получить двумя способами: создать его самостоятельно или вызвать метод `Indexes.GetClient` в `SearchServiceClient`. Для удобства мы используем второй способ.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> В типичном поисковом приложении Управление индексами и заполнение может быть обработано отдельным компонентом из запросов поиска. `Indexes.GetClient` удобно использовать для заполнения индекса, потому что избавляет от необходимости указывать дополнительные `SearchCredentials`. Это достигается благодаря передачи ключа администратора, который использовался для создания `SearchServiceClient`, в новый элемент `SearchIndexClient`. Тем не менее, в части приложения, которое выполняет запросы лучше создать `SearchIndexClient` непосредственно таким образом, вы можете передать ключ запроса, который можно только для чтения данных, а не ключ администратора. Это согласуется с принципом наименьших прав доступа и поможет сделать приложение более безопасным. Дополнительные сведения о ключах администраторов и запросов см. [здесь](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Теперь, когда у нас есть `SearchIndexClient`, мы можем заполнить индекс. Заполнение индекса выполняется с помощью другого метода, который мы рассмотрим позже.

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

Если запустить это приложение с действительными именем службы и ключи API, результат должен выглядеть как в следующем примере: (Некоторые выходные данные консоли был заменен «...» для наглядности.)

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
После создания `SearchServiceClient`, `Main` удаляет индекс «hotels», если он уже существует. Удаление выполняется следующим способом:

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
> Для простоты пример кода в этой статье использует синхронные методы пакета SDK для Поиска Azure в .NET. Рекомендуем использовать в ваших приложениях асинхронные методы, чтобы сохранить масштабируемость и скорость отклика. Например, в приведенном выше методе можно использовать `ExistsAsync` и `DeleteAsync` вместо `Exists` и `Delete`.
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

Помимо полей можно также добавить профили оценки, средства подбора или параметры CORS к индексу (эти параметры не указаны в примере для краткости). Дополнительные сведения об объекте Index и его составных частях см. в [справочнике по SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), а также в [справочнике по REST API службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Заполнение индекса
Следующий шаг в `Main` заполняет индекс только что созданный. Это заполнение индекса выполняется в следующий метод: (Некоторые кода заменен на «...» для наглядности.  См. Полный пример решения для заполнения код полного набора данных).

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

Этот метод состоит из четырех частей. Первая создает массив из 3 `Hotel` объектов, каждый из которых 3 `Room` объекты, которые будут служить входными данными для отправки в индекс. Эти данные жестко запрограммированы для простоты. В вашем приложении данные скорее всего будут поступать из внешнего источника, например базы данных SQL.

Вторая часть создает `IndexBatch` , содержащий документы. Вы указываете операцию, которую необходимо применить к пакету во время его создания, в данном случае путем вызова метода `IndexBatch.Upload`. Затем этот пакет отправляется в индекс службы поиска Azure с помощью метода `Documents.Index` .

> [!NOTE]
> В этом примере мы просто отправляем документы. Если требуется внести изменения в существующие документы или удалить документы, можно создать пакеты, вызвав методы `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` или `IndexBatch.Delete`. Можно также смешать различные операции в одном пакете, вызвав метод `IndexBatch.New`, принимающий коллекцию объектов `IndexAction`, каждый из которых указывает Поиску Azure выполнять определенную операцию с документом. Можно создать каждый `IndexAction` с отдельной операцией, вызвав соответствующий метод, например `IndexAction.Merge`, `IndexAction.Upload` и т. д.
> 
> 

Третья часть метода — это блок catch, который обрабатывает важные ошибки индексирования. Если службе поиска Azure не удается индексировать некоторые документы в пакете, `Documents.Index` вызывает `IndexBatchException`. Это исключение может произойти, если вы индексируете документы службы при интенсивной нагрузке. **Настоятельно рекомендуется явно обрабатывать этот случай в коде.** Вы можете задержать и повторить попытку индексирования соответствующих документов либо занести ошибку в журнал и продолжить работу, как в нашем примере, а также выполнить другие действия в зависимости от требований вашего приложения к целостности данных.

> [!NOTE]
> С помощью метода [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) вы можете создать новый пакет, содержащий только те действия, которые не удалось выполнить в предыдущем вызове метода `Index`. На сайте [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry) опубликовано обсуждение правил его использования.
>
>

И наконец, метод `UploadDocuments` вызывает задержку на две секунды. Индексирование в службе поиска Azure происходит асинхронно, поэтому образец приложения должен подождать немного, пока документы не станут доступными для поиска. Такие задержки обычно необходимы только в демонстрациях, тестах и примерах приложений.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Обработка документов пакетом SDK для .NET
Вас может интересовать, как пакет SDK для Поиска Azure в .NETможет передавать экземпляры пользовательского класса, например `Hotel` , в индекс. Чтобы ответить на этот вопрос, рассмотрим класс `Hotel` :

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

Прежде всего следует отметить это имя каждое общедоступное свойство в `Hotel` класс будет сопоставить поле с тем же именем, в определении индекса. Вы хотите каждого поля, чтобы начать со строчной буквы («верблюжий»), можно указать пакет SDK для сопоставления имен свойств в нижнем регистре, автоматически с помощью `[SerializePropertyNamesAsCamelCase]` атрибут класса. Этот сценарий характерен для приложений .NET, выполняющих привязку данных, где в целевой схеме — за пределами элемента управления, разработчик приложения без необходимости нарушают правила в .NET именования «верхнего регистра».

> [!NOTE]
> Пакет SDK службы поиска Azure для .NET использует библиотеку [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) для сериализации и десериализации настраиваемых объектов модели в JSON и обратно. При необходимости эту сериализацию можно настроить. Дополнительные сведения см. в разделе [пользовательская сериализация с помощью JSON.NET](#JsonDotNet).
> 
> 

Вторая вещь, обратите внимание, — каждое свойство снабжен атрибуты, такие как `IsFilterable`, `IsSearchable`, `Key`, и `Analyzer`. Эти атрибуты сопоставляются непосредственно с [атрибуты соответствующего поля в индекс службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/create-index#request). `FieldBuilder` Класс использует эти свойства для создания определения полей для индекса.

Третий важный аспект `Hotel` класс — это типы данных из открытых свойств. Типы .NET этих свойств сопоставляются с эквивалентными типами полей в определении индекса. Например, свойство строки `Category` сопоставляется с полем `category`, которое имеет тип `Edm.String`. Аналогичные сопоставления присутствуют типами между `bool?`, `Edm.Boolean`, `DateTimeOffset?`, и `Edm.DateTimeOffset` и т. д. Конкретные правила сопоставления типов указаны в описании метода `Documents.Get` в [справочнике по SDK для .NET службы поиска Azure](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). Хотя класс `FieldBuilder` выполняет это сопоставление, этот механизм полезно понимать на случай, если вам понадобится устранять неполадки, связанные с сериализацией.

Случилось Обратите внимание, что `SmokingAllowed` свойство?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

`JsonIgnore` Атрибут на это свойство сообщает `FieldBuilder` не сериализовать данные в индекс как поле.  Это прекрасный способ создания клиентского вычисляемые свойства, которые можно использовать в качестве вспомогательных функций в приложении.  В этом случае `SmokingAllowed` свойство отражает ли какая-либо `Room` в `Rooms` коллекции позволяет курящих.  Если все имеют значение false, это означает, что весь отеля запрещены курящих.

Некоторые свойства, такие как `Address` и `Rooms` являются экземплярами классов .NET.  Эти свойства представляют более сложные структуры данных и, таким образом, требует полей с [сложный тип данных](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) в индексе.

`Address` Свойство представляет собой набор нескольких значений в `Address` класс, определенный ниже:

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

Этот класс содержит стандартные значения, используемые для описания адреса в США или Канаде. Типы следующим образом можно использовать для группировки логического поля в индексе.

`Rooms` Свойство представляет собой массив `Room` объектов:

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

Модель данных в .NET и его соответствующие схемы индекса должны разрабатываться для поддержки результатов поиска, которые вы хотите предоставить конечному пользователю. Каждый объект верхнего уровня в .NET, ie документов в индексе, соответствует результат поиска, которые должны выводиться в пользовательском интерфейсе. Например в приложении поиска hotel конечным пользователям может потребоваться поиск по название отеля, функции гостиницы или характеристики определенном помещении. Будут рассмотрены некоторые примеры запросов немного позже.

Возможность использовать собственные классы для взаимодействия с документов в индексе, работает в обоих направлениях; Можно также получать результаты поиска и установить пакет SDK автоматическую десериализацию к типу по своему усмотрению, в том случае, как мы увидим в следующем разделе.

> [!NOTE]
> Пакет SDK для Поиска Azure в .NET также поддерживает документы динамических типов, поддерживающие класс `Document`, то есть сопоставление ключей и значений с именами и значениями полей. Это полезно в тех случаях, когда во время разработки вам неизвестна схема индекса или привязка к конкретным классам моделей нецелесообразна. Все методы пакета SDK, связанные с документами, имеют перегрузки, работающие с классом `Document`, а также строго типизированные перегрузки, принимающие параметр универсального типа. Пример кода в данном руководстве содержит только перегрузки второго типа. Класс [`Document` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document)наследует от `Dictionary<string, object>`.
> 
>

**Почему следует использовать типы данных, допускающие значение NULL**

При разработке собственных классов модели для сопоставления с индексом поиска Azure рекомендуется объявлять свойства типов значений, такие как `bool` и `int`, допускающих нулевое значение (например: `bool?` вместо `bool`). При использовании ненулевого свойства необходимо **гарантировать** , что документы в индексе не будут содержать значение NULL для соответствующего поля. Ни пакет SDK, ни служба поиска Azure не помогут вам это обеспечить.

Это не просто гипотетическое соображение. Представьте себе ситуацию, когда вы добавляете новое поле в имеющийся индекс с типом `Edm.Int32`. После обновления определения индекса все документы будут иметь значение null для этого нового поля (поскольку все типы допускают значение NULL в службе поиска Azure). Если затем для этого поля вы используете класс модели со свойством `int`, не допускающим нулевое значение, при попытке получения документов вы получите `JsonSerializationException` следующим образом:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

По этой причине по-прежнему рекомендуется использовать типы, допускающие значения NULL, в классах модели.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Пользовательская сериализация с помощью JSON.NET
Пакет SDK использует JSON.NET для сериализации и десериализации документов. Вы можете настроить сериализацию и десериализацию, при необходимости, определив собственные `JsonConverter` или `IContractResolver`. Дополнительные сведения см. в разделе [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm). Это может быть полезно, если нужно адаптировать существующий класс модели из приложения для использования с поиском Azure и других более сложных сценариев. Например, с помощью пользовательской сериализации можно делать следующее.

* Включить или исключить определенные свойства класса модели с сохранением в виде поля документа.
* Реализовать сопоставление между именами свойств в коде и именами полей в индексе.
* Создать настраиваемые атрибуты, которые можно использовать для сопоставления свойств с полями документов.

Примеры реализации пользовательской сериализации можно найти в модульных тестах к пакету SDK для .NET для поиска Azure на GitHub. Хорошей отправной точкой будет [эта папка](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Она содержит классы, используемые в тестах пользовательской сериализации.

### <a name="searching-for-documents-in-the-index"></a>Поиск документов в индексе
Последний шаг в пример приложения — поиск некоторых документов в индексе:

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

При каждом выполнении запроса этот метод прежде всего создает объект `SearchParameters`. Этот объект используется для указания дополнительных параметров запроса, например сортировка, фильтрация, разбиение по страницам и фасетная Навигация. В этом методе мы задаем свойства `Filter`, `Select`, `OrderBy` и `Top` для разных запросов. Все свойства `SearchParameters` описаны [здесь](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Следующий этап — фактическое выполнение поискового запроса. Где выполняется поиск выполняется с помощью `Documents.Search` метод. Для каждого запроса мы передаем текст для поиска в строковом формате (или `"*"`, если текст для поиска отсутствует) и созданные ранее параметры поиска. Мы также указываем `Hotel` как параметр типа `Documents.Search`, в результате чего пакет SDK будет десериализовать документы в результатах поиска к объектам типа `Hotel`.

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

В этом случае идет поиск по всему индексу для слова «motel» в любом поле, для поиска, и мы хотим получить имена гостиницы, в соответствии с `Select` параметра. Вот результат выполнения:

    Name: Secret Point Motel

    Name: Twin Dome Motel

Следующий запрос более интересным.  Нам нужно найти все гостиницы, помещение с менее чем 100 долл. США за ночь и возвращают только hotel идентификатор и описание:

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

Этот запрос использует выражение OData `$filter` (по значению `Rooms/any(r: r/BaseRate lt 100)`), чтобы отфильтровать в индексе нужные документы. При этом используется [любой оператор](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) для применения "lt базовым тарифом BaseRate 100' для каждого элемента в коллекции комнаты. Дополнительные сведения о синтаксисе OData, который поддерживает служба поиска Azure, см. [здесь](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

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

Вот результат выполнения:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Наконец нам нужно найти все гостиницы имена, которые соответствуют слову «отель»:

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

На этом руководство заканчивается, но вам не стоит останавливаться. ** Далее приведены дополнительные ресурсы для получения дополнительных сведений о службе поиска Azure.

## <a name="next-steps"></a>Дальнейшие действия
* Изучите справочную информацию о [пакете SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) и [REST API](https://docs.microsoft.com/rest/api/searchservice/).
* Изучите [соглашения о наименовании](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) , чтобы узнать правила именования различных объектов.
* Изучите [поддерживаемые типы данных](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) в службе поиска Azure.
