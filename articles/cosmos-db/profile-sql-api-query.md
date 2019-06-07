---
title: Получить метрики производительности и исполнение запросов SQL
description: Узнайте, как получить метрики выполнения запросов SQL и профиль производительности запросов SQL запросов Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: b4017666956d0e01ea19781fb4f1ce2dde15fff5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481569"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Получить метрики выполнения SQL-запросов и анализ производительности запросов с помощью пакета SDK для .NET

В этой статье представлены профилирование производительности запросов SQL в Azure Cosmos DB. Профилирование, это можно сделать с помощью `QueryMetrics` извлекается из пакета SDK для .NET и подробно описан ниже. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) — это строго типизированный объект с информацией о выполнении запроса серверной части. Эти метрики описаны более подробно в [Настройка производительности запросов](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) статьи.

## <a name="set-the-feedoptions-parameter"></a>Задайте для параметра FeedOptions

Все перегрузки для [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) примите в необязательный [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) параметра. Этот параметр в том, что позволяет выполнение запроса отрегулировать и параметризованные. 

Чтобы собирать метрики выполнения запросов Sql, необходимо указать параметр [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) в [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) для `true`. Установка `PopulateQueryMetrics` для ИСТИНА сделает ее, чтобы `FeedResponse` будет содержать соответствующие `QueryMetrics`. 

## <a name="get-query-metrics-with-asdocumentquery"></a>Получить метрики запросов с AsDocumentQuery()
В следующем образце кода показано, как получить метрики, при использовании [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) метод:

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
## <a name="aggregating-querymetrics"></a>Статистическая обработка QueryMetrics

В предыдущем разделе, обратите внимание на то, что было несколько вызовов [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) метод. Каждый вызов вернул `FeedResponse` объект, имеющий словарь `QueryMetrics`— одна для каждого продолжения запроса. В следующем примере показано, как для статистической обработки этих `QueryMetrics` с помощью LINQ:

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

## <a name="grouping-query-metrics-by-partition-id"></a>Метрики запросов группирование по Идентификатору секции

Вы можете сгруппировать `QueryMetrics` , идентификатор секции. Группирование по Идентификатору секции можно видеть, если определенный раздел является причиной проблем с производительностью по сравнению с другими пользователями. Следующий пример показывает способ группировки `QueryMetrics` с помощью LINQ:

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

## <a name="linq-on-documentquery"></a>LINQ на DocumentQuery

Можно также получить `FeedResponse` из запроса LINQ с помощью `AsDocumentQuery()` метод:

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

Вы можете собирать единиц запросов по каждому запросу для изучения ресурсоемких запросов или запросов, использующих высокой пропускной способностью. Единиц запроса можно получить с помощью [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) свойство в `FeedResponse`. Дополнительные сведения о том, как получить единиц запроса с помощью портала Azure и разные пакеты SDK, см. в разделе [затраты единиц запросов на поиск](find-request-unit-charge.md) статьи.

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

## <a name="get-the-query-execution-time"></a>Получить время выполнения запроса

При вычислении время, необходимое для выполнения клиентского запроса, убедитесь, что вы включить только время для вызова `ExecuteNextAsync` метода и не других частей базы кода. Только эти вызовы, чтобы помочь в вычислении, сколько времени заняло выполнение запроса, как показано в следующем примере:

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>Проверять запросы (обычно слишком медленной и дорогостоящей)

Запрос просмотра ссылается на запрос, который не был обработан по индексу, из-за чего, многие документы загружаются перед возвратом результирующего набора.

Ниже приведен пример запроса сканирования:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Фильтр этот запрос использует системную функцию UPPER и не получены из индекса. Выполнение этого запроса на основе обширной коллекции создаются следующие метрики для первого продолжения:

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

Обратите внимание на следующие значения, из выходных данных запроса метрики:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Загрузить этот запрос 60,951 документы, которые сгруппированы 399,998,938 байт. Загрузка указанного количества байтов приводит к высокой стоимости или запроса на затраты единиц. Она также занимает много времени для выполнения запроса, который снят со свойством общее время, затраченное:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Это означает, для выполнения запроса потребовалось 4,5 секунд выполнения (и это было только одно продолжение).

Чтобы оптимизировать этот пример запроса, избегайте использования ВЕРХНЕЙ в фильтре. Вместо этого при документов при создании или обновлении, `c.description` значения должен быть вставлен в всех символов верхнего регистра. Запрос становится: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Этот запрос теперь имеет возможность быть получены из индекса.

Дополнительные сведения о настройке производительности запросов, см. в разделе [Настройка производительности запросов](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) статьи.

## <a id="References"></a>Справочные материалы

- [DocumentDB SQL Syntax](https://go.microsoft.com/fwlink/p/?LinkID=510612) (Синтаксис SQL в DocumentDB)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка производительности запросов](sql-api-query-metrics.md)
- [Общие сведения об индексировании](index-overview.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
