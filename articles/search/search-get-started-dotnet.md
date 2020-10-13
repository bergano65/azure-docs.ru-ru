---
title: Краткое руководство. Создание индекса поиска в .NET
titleSuffix: Azure Cognitive Search
description: В этом руководстве по C# показано, как создать индекс, загрузить данные и выполнить запросы с помощью клиентской библиотеки Azure.Search.Documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 53deb7dc853de969ad6b6679ee728a3f132b6309
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759096"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Краткое руководство. Создание индекса поиска с помощью клиентской библиотеки Azure.Search.Documents

С помощью новой [клиентской библиотеки Azure.Search.Documents (версия 11)](/dotnet/api/overview/azure/search.documents-readme) вы можете создать консольное приложение .NET Core на языке C#, которое создает, загружает и запрашивает индекс поиска.

[Скачайте исходный код](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11), чтобы начать работу с готовым проектом, или выполните описанные в этой статье действия, чтобы создать собственный проект.

> [!NOTE]
> Вам нужна более ранняя версия? См. статью [Создание индекса поиска с помощью клиентской библиотеки Microsoft.Azure.Search версии 10](search-get-started-dotnet-v10.md).

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать работу, убедитесь, что у вас есть следующие средства и службы:

+ Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/) бесплатно.

+ Служба "Когнитивный поиск Azure". [Создайте новую](search-create-service-portal.md) или [найдите существующую службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом кратком руководстве. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) (любой выпуск). Пример кода был протестирован в Visual Studio 2019 (бесплатный выпуск Community).

+ [Пакет NuGet Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)

## <a name="set-up-your-project"></a>Настройка проекта

Соберите сведения о подключении к службе, а затем откройте Visual Studio, чтобы создать новый проект консольного приложения, который можно запустить в .NET Core.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Копирование ключа и конечной точки

Вызовы к службе требуют конечную точку URL-адреса и ключ доступа при каждом запросе. В первую очередь найдите ключ API и URL-адрес для добавления в проект. При создании клиента на более позднем этапе необходимо указать оба значения.

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

2. В разделе **Параметры** > **Ключи** получите ключ администратора с полным доступом к службе, требуемый для создания и удаления объектов. Существует две взаимозаменяемых пары первичного и вторичного ключей. Вы можете использовать любую из этих пар.

   ![Получение конечной точки HTTP и ключа доступа](media/search-get-started-postman/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Для выполнения любого запроса к службе требуется использование ключа API. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

### <a name="install-the-nuget-package"></a>Установка пакета NuGet

После создания проекта добавьте клиентскую библиотеку. [Пакет Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) состоит из одной клиентской библиотеки, которая предоставляет все API для работы со службой поиска в .NET.

1. В разделе **Инструменты** > **Диспетчер пакетов NuGet** выберите  **Управление пакетами NugGet для решения...** . 

1. Нажмите кнопку **Обзор**.

1. Найдите `Azure.Search.Documents` и выберите версию 11.0 или более позднюю.

1. Щелкните **Установить** справа, чтобы добавить сборку в проект и решение.

### <a name="create-a-search-client"></a>Создание клиента для поиска

1. В файле **Program.cs** измените пространство имен на `AzureSearch.SDK.Quickstart.v11`, а затем добавьте следующие директивы `using`.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Создайте два клиента: [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) создает индекс, а [SearchClient](/dotnet/api/azure.search.documents.searchclient) загружает и запрашивает существующий индекс. Обоим клиентам нужны конечная точка службы и ключ API администрирования, чтобы пройти проверку подлинности и получить права на создание и удаление.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1\. Создание индекса

В этом кратком руководстве показано, как создать индекс Hotels, в который вы позже загрузите данные об отелях и к которому будете выполнять запросы. На этом шаге следует определить поля в этом индексе. Каждое определение поля содержит имя, тип данных и атрибуты, которые определяют способ использования этого поля.

В нашем примере для простоты и удобочитаемости используются синхронные методы библиотеки Azure.Search.Documents. Но для рабочих сценариев лучше использовать асинхронные методы, чтобы приложение было масштабируемым и отзывчивым. Например, следует использовать [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) вместо [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Добавьте в проект два пустых определения классов: **Hotel.cs**

1. В **Hotel.cs** определите структуру документа с данными об отелях.

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. В **Program.cs** создайте объект [SearchClient](/dotnet/api/azure.search.documents.indexes.models.searchindex), а затем вызовите метод [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex), чтобы отразить индекс в службе поиска.

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

Атрибуты поля определяют его использование в приложении. Например, атрибут `IsFilterable` должен быть присвоен каждому полю, которое поддерживает выражение фильтра.

В клиентской библиотеке Azure.Search.Documents можно использовать [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) и [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) для упрощения определения полей. Оба метода являются производными от [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) и потенциально могут упростить код:

+ `SimpleField` может быть любым типом данных, он всегда недоступен для поиска (он игнорируется для запросов полнотекстового поиска) и его можно извлечь (он не скрыт). Другие атрибуты отключены по умолчанию, но их можно включить. `SimpleField` можно использовать для идентификаторов документов или полей, используемых только в фильтрах, аспектах или профилях оценки. Если это так, обязательно примените все необходимые для сценария атрибуты, например `IsKey = true` для идентификатора документа. Чтобы узнать больше, см. [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) в исходном коде.

+ `SearchableField` должен быть строкой и всегда доступен для поиска и извлечения. Другие атрибуты отключены по умолчанию, но их можно включить. Так как этот тип поля доступен для поиска, он поддерживает синонимы и полное дополнение свойств анализатора. Чтобы узнать больше, см. [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) в исходном коде.

Независимо от того, используется ли базовый API `SearchField` или одна из вспомогательных моделей, необходимо явно включить атрибуты фильтров, аспектов и сортировки. Например, [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) и [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) должны присваиваться явным образом, как в примере выше. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2\. Загрузка документов

Когнитивный поиск Azure выполняет поиск по содержимому, которое хранится в этой службе. На этом шаге вы отправите документы JSON, которые соответствуют формату созданного индекса отелей.

Искомые документы в службе "Когнитивный поиск Azure" представляют собой структуры данных, которые служат входными данными для индексирования и (или) результатами запросов. Полученные из внешнего источника данных входные документы могут содержать строки базы данных, большие двоичные объекты из хранилища BLOB-объектов или сохраненные на диске документы JSON. В нашем примере мы выбрали самый простой путь, внедрив прямо в код документы JSON с данными о пяти отелях. 

При отправке документов необходимо использовать объект [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1). Объект `IndexDocumentsBatch` содержит коллекцию [действий](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), каждое из которых содержит документ и свойство с описанием действия, которое должен выполнить Когнитивный поиск Azure ([отправка, объединение, удаление и mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. В файле **Program.cs** создайте массив документов и действий индексирования, а затем передайте этот массив в `IndexDocumentsBatch`. Приведенные ниже документы соответствуют индексу hotels-quickstart-v11, как определено в классе hotel.

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    srchclient.IndexDocuments(batch, idxoptions);
    ```

    Создав экземпляр объекта [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1), вы сможете отправить его в индекс, вызвав [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) из объекта [SearchClient](/dotnet/api/azure.search.documents.searchclient).

1. Так как это консольное приложение выполняет все команды последовательно, включите 2-секундные паузы между операциями индексирования и запросов.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    2-секундная задержка дает достаточно времени для асинхронного индексирования, чтобы все документы уже были проиндексированы перед выполнением запросов. Задержки в коде обычно используются только в демонстрациях, тестах и примерах приложений.

## <a name="3---search-an-index"></a>3\. Поиск в индексе

Результаты запросов можно получить сразу по завершении индексирования первого документа, но для полноценного тестирования индекса придется подождать, пока закончится индексирование всех документов.

В этом разделе мы добавим две новые функции: логику запроса и результаты. Для запросов примените метод [Search](/dotnet/api/azure.search.documents.searchclient.search). Этот метод принимает текст для поиска (строку запроса) и другие [параметры](/dotnet/api/azure.search.documents.searchoptions).

Класс [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) представляет результаты запроса.

1. В файле **Program.cs** создайте метод WriteDocuments, который выводит в консоль результаты поиска.

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. Создайте метод RunQueries для выполнения запросов и возврата результатов. Результаты имеют формат объектов Hotel.

    ```csharp
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = srchclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

1. Добавьте RunQueries в `Main()`.

    ```csharp
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);
    ```

Существует два [способа сопоставления терминов в запросе](search-query-overview.md#types-of-queries): полнотекстовый поиск и фильтры.

+ Полнотекстовый поисковый запрос ищет один или несколько терминов в полях индекса с поддержкой поиска. Первый из запросов здесь является полнотекстовым. Полнотекстовый поиск возвращает оценки релевантности для ранжирования результатов.

+ Фильтром называется логическое выражение, которое оценивается для всех полей в индексе с атрибутом [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable). Фильтрующие запросы могут включать или исключать указанные значения. Поэтому для фильтрующего запроса не существует оценки релевантности. Последние два запроса здесь демонстрируют использование фильтра.

Полнотекстовый поиск и фильтры можно использовать вместе или по отдельности.

Поиск и фильтрация выполняются с помощью метода [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search). Строку поиска можно передать в параметре `searchText`, а выражение фильтра — свойстве [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) класса [SearchOptions](/dotnet/api/azure.search.documents.searchoptions). Чтобы выполнить фильтрацию без поиска, передайте `"*"` в качестве значения параметра `searchText` в метод [Search](/dotnet/api/azure.search.documents.searchclient.search). Чтобы выполнить поиск без фильтрации, не задавайте значение свойству `Filter` или вообще не передавайте экземпляр `SearchOptions`.

## <a name="run-the-program"></a>Запуск программы

Нажмите клавишу F5, чтобы перестроить приложение и запустить полнофункциональную программу. 

Выходные данные содержат сообщения из [Console.WriteLine](/dotnet/api/system.console.writeline), а также сведения о запросе и результаты.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы работаете в своей подписке, по окончании проекта рекомендуем решить, нужны ли вам созданные ресурсы. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Просматривать ресурсы и управлять ими можно на портале с помощью ссылок **Все ресурсы** или **Группы ресурсов** на панели навигации слева.

При работе с бесплатной версией службы помните о том, что вам доступно максимум три индекса, индексатора и источника данных. Вы можете удалить отдельные элементы на портале, чтобы не превысить лимит. 

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве для C# вы отработали задачи по созданию индекса, загрузке документов в него и выполнению запросов. Мы несколько упростили решение, чтобы код было проще читать и понимать. Если основные понятия вы усвоили хорошо, мы рекомендуем перейти к следующей статье для изучения альтернативных подходов и основных понятий, которые дополнят и расширят ваши знания. 

> [!div class="nextstepaction"]
> [Разработка в .NET](search-howto-dotnet-sdk.md)

Хотите оптимизировать и сократить ваши расходы на облако?

> [!div class="nextstepaction"]
> [Начните анализировать затраты с помощью службы "Управление затратами"](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)