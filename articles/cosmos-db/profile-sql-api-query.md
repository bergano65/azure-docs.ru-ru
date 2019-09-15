---
title: Получение метрик выполнения SQL Query &
description: Узнайте, как получить метрики выполнения запросов SQL и пропрофилировать производительность запросов SQL Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998364"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Получение метрик выполнения запросов SQL и анализ производительности запросов с помощью пакета SDK для .NET

В этой статье описывается, как профилировать производительность запросов SQL на Azure Cosmos DB. Это профилирование можно выполнить с помощью `QueryMetrics` команды, полученной из пакета SDK для .NET, и подробно описано здесь. [Куериметрикс](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) — это строго типизированный объект со сведениями о выполнении внутреннего запроса. Эти метрики более подробно описаны в статье [Настройка производительности запросов](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) .

## <a name="set-the-feedoptions-parameter"></a>Задание параметра FeedOptions

Все перегрузки для [DocumentClient. CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) принимают необязательный параметр [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) . Этот параметр позволяет настроить и параметризованное выполнение запроса. 

Для сбора метрик выполнения запросов SQL необходимо задать параметру [популатекуериметрикс](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) в `true` [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) значение. Если `PopulateQueryMetrics` задано значение true, то `FeedResponse` будет содержать соответствующий `QueryMetrics`объект. 

## <a name="get-query-metrics-with-asdocumentquery"></a>Получение метрик запроса с помощью Асдокументкуери ()
В следующем примере кода показано, как получить метрики при использовании метода [асдокументкуери ()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) :

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Статистическая обработка Куериметрикс

В предыдущем разделе Обратите внимание, что существовало несколько вызовов метода [ексекутенекстасинк](https://msdn.microsoft.com/library/azure/dn850294.aspx) . Каждый вызов вернул `FeedResponse` объект с `QueryMetrics`словарем; по одному для каждого продолжения запроса. В следующем примере показано, как выполнить статистическую обработку `QueryMetrics` с помощью LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Группирование метрик запроса по ИДЕНТИФИКАТОРу секции

Можно сгруппировать `QueryMetrics` по идентификатору секции. Группировка по ИДЕНТИФИКАТОРу секции позволяет определить, вызывают ли определенные секции проблемы с производительностью по сравнению с другими. В следующем примере показано, как группировать `QueryMetrics` с помощью LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ на Документкуери

Можно также получить `FeedResponse` из запроса LINQ `AsDocumentQuery()` с помощью метода:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Ресурсоемкие запросы

Можно записать единицы запросов, потребляемые каждым запросом, чтобы исследовать ресурсоемкие запросы или запросы, использующие высокую пропускную способность. Плату за запрос можно получить с помощью свойства [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) в `FeedResponse`. Дополнительные сведения о том, как получить оплату запросов с помощью портал Azure и различных пакетов SDK, см. в статье найти статью о [расходах единиц запросов](find-request-unit-charge.md) .

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Получение времени выполнения запроса

При вычислении времени, необходимого для выполнения запроса на стороне клиента, убедитесь, что включается только время для вызова `ExecuteNextAsync` метода, а не других частей базы кода. Только эти вызовы помогают вычислить длительность выполнения запроса, как показано в следующем примере:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Сканировать запросы (обычно замедляют и ресурсоемкий)

Запрос на сканирование ссылается на запрос, который не был обслужен индексом, из-за чего перед возвратом результирующего набора загружается много документов.

Ниже приведен пример запроса сканирования.

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

В фильтре запроса используется системная функция UPPER, которая не обрабатывается из индекса. При выполнении этого запроса к большой коллекции были получены следующие метрики запроса для первого продолжения:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Обратите внимание на следующие значения из выходных данных метрик запроса:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Этот запрос загрузил 60 951 документов, которые были суммированы 399 998 938 байт. Загрузка этого большого числа байтов приводит к высокой стоимости и затратам единиц запросов. Выполнение запроса также занимает много времени, что ясно, когда общее время, затраченное на свойство:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Это означает, что выполнение запроса заняло 4,5 секунд (и это было только одно продолжение).

Чтобы оптимизировать этот пример запроса, старайтесь не использовать верхний в фильтре. Вместо этого при создании или обновлении `c.description` документов необходимо вставлять значения во все символы верхнего регистра. Затем запрос выглядит следующим образом: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Теперь этот запрос может быть обслужен из индекса.

Дополнительные сведения о настройке производительности запросов см. в статье [Настройка производительности запросов](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) .

## <a id="References"></a>Справочные материалы

- [DocumentDB SQL Syntax](https://go.microsoft.com/fwlink/p/?LinkID=510612) (Синтаксис SQL в DocumentDB)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Следующие шаги

- [Настройка производительности запросов](sql-api-query-metrics.md)
- [Общие сведения об индексировании](index-overview.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
