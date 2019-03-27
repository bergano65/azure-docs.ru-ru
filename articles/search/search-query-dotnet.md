---
title: Запрос данных из индекса Поиска Azure на C# (пакет SDK для .NET) — Поиск Azure
description: Пример кода C# для создания поискового запроса в Поиске Azure. Добавление параметров поиска для фильтрации и сортировки результатов поиска.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 6bb170a5f3353288ab9c393e01b7a0902361913b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287015"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>Краткое руководство. 3 — запрос индекса Поиска Azure на C#

В этой статье показано, как выполнять запрос [индекса Поиска Azure](search-what-is-an-index.md) с использованием C# и [пакета SDK для .NET](https://aka.ms/search-sdk). Поиск документов в индексе выполняется с помощью следующих задач:

> [!div class="checklist"]
> * Создайте объект [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet), который будет подключен к индексу поиска с правами только для чтения.
> * Создайте объект [`SearchParameters`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet), содержащий определение поиска или фильтра.
> * Вызовите метод `Documents.Search` в классе `SearchIndexClient` для отправки запросов в индекс.

## <a name="prerequisites"></a>Предварительные требования

[Загрузите индекс Поиска Azure](search-import-data-dotnet.md) с демонстрационными данными гостиниц.

Получите ключ запроса, используемый для доступа только для чтения к документам. До сих пор вы использовали ключ API администратора, чтобы можно было создавать объекты и содержимое в службе поиска. Но для поддержки запросов в приложениях мы настоятельно рекомендуем использовать ключ запроса. Инструкции см. в разделе о [создании ключей запросов](search-security-api-keys.md#create-query-keys).

## <a name="create-a-client"></a>Создание клиента
Создайте экземпляр класса `SearchIndexClient`, чтобы ему можно было присвоить ключ запроса для доступа только для чтения (в отличие от прав доступа для записи, предоставленных `SearchServiceClient`, используемому в предыдущем уроке).

Этот класс имеет несколько конструкторов. Нужный вам конструктор принимает в качестве параметров имя службы поиска, имя индекса и объект [`SearchCredentials`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet). `SearchCredentials` содержит ключ API.

Приведенный ниже код создает `SearchIndexClient` для индекса hotels с использованием значений для имени службы поиска и ключа API, которые хранятся в файле конфигурации приложения (`appsettings.json` в случае [примера приложения](https://aka.ms/search-dotnet-howto)):

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

У класса `SearchIndexClient` есть свойство [`Documents`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet). Это свойство предоставляет все методы, которые требуются для отправки запросов в индексы службы поиска Azure.

## <a name="construct-searchparameters"></a>Создание объекта SearchParameters
Поиск с помощью пакета SDK для .NET сводится к вызову метода `Documents.Search` для класса `SearchIndexClient`. Этот метод принимает несколько параметров, включая текст поиска и объект `SearchParameters` , с помощью которого можно уточнить запрос.

### <a name="types-of-queries"></a>Типы запросов
Два основных [типа запросов](search-query-overview.md#types-of-queries), которые вы будете использовать, — это `search` и `filter`. Запрос `search` выполняет поиск по одному или нескольким условиям во всех *поддерживающих поиск* полях индекса. Запрос `filter` оценивает логическое выражение во всех *фильтруемых* полях индекса. Запросы этих двух типов можно использовать вместе или по отдельности.

Поиск и фильтрация выполняются с помощью метода `Documents.Search` . Поисковый запрос можно передать в параметре `searchText`, а выражение фильтра — в свойстве `Filter` класса `SearchParameters`. Чтобы выполнить фильтрацию и при этом не выполнять поиск, в качестве значения параметра `searchText` укажите `"*"`. Чтобы выполнить поиск, не прибегая к фильтрации, не задавайте значение свойства `Filter` или вообще не передавайте его в экземпляр `SearchParameters`.

### <a name="example-queries"></a>Примеры запросов
В следующем примере кода показано несколько разных способов отправки запросов в индекс hotels, описанный в статье о [создании индекса Поиска Azure на C#](search-create-index-dotnet.md#DefineIndex). Обратите внимание, что документы, возвращаемые в результатах поиска, — это экземпляры класса `Hotel`, описанного в статье об [импорте данных в индекс Поиска Azure на C#](search-import-data-dotnet.md#construct-indexbatch). Пример кода выводит результаты поиска на консоль с помощью метода `WriteDocuments` . Этот метод описан в следующем разделе.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>Обработка результатов поиска
Метод `Documents.Search` возвращает объект `DocumentSearchResult`, содержащий результаты запроса. В примере из предыдущего раздела результаты поиска выводятся на консоль с помощью метода `WriteDocuments` .

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

Вот как будут выглядеть результаты запросов из предыдущего раздела (если индекс hotels заполнен демонстрационными данными):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

Приведенный выше пример кода выводит результаты поиска в консоль. Таким же образом вам нужно будет показывать результаты поиска и в вашем приложении. Пример того, как отображать результаты поиска в веб-приложении ASP.NET с использованием схемы MVC, см. в [проекте DotNetSample](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) на сайте GitHub.

## <a name="next-steps"></a>Дополнительная информация

Если вы еще этого не сделали, просмотрите пример кода в [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) на сайте GitHub, а также ознакомьтесь со статьей [Использование службы поиска Azure в приложении .NET](search-howto-dotnet-sdk.md), чтобы получить более подробное описание примеров кода. 