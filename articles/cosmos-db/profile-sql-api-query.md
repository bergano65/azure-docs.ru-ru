---
title: Получите производительность производительности запроса & производительности s'L
description: Узнайте, как получить метрики выполнения запросов запросов запросов запросов запросов Запросов Azure Cosmos DB, а также производительность выполнения запросов запросов Запросов Azure Cosmos DB, а также производительность запросов запросов запросов Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70998364"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Получите метрики выполнения запросов и проанализируйте производительность запроса с помощью .NET SDK

В этой статье представлено, как профилировать производительность запроса S'L на Azure Cosmos DB. Это профилирование может `QueryMetrics` быть сделано с помощью извлеченных из .NET SDK и подробно описано здесь. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) — это сильно набранный объект с информацией о выполнении запроса бэкэнда. Эти показатели более подробно описаны в статье [Tune Query Performance.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="set-the-feedoptions-parameter"></a>Установите параметр FeedOptions

Все перегрузки для [DocumentClient.CreateДокументокери](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) принимают в дополнительном [параметре FeedOptions.](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) Эта опция позволяет настроить и параметризировать выполнение запроса. 

Для сбора метрик выполнения запроса Sql необходимо установить параметр [Populate-EryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) в [FeedOptions.](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) `true` Установка `PopulateQueryMetrics` на истину сделает `FeedResponse` это так, `QueryMetrics`что будет содержать соответствующие . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Получить метрики запроса с AsDocument'ery()
В следующем примере кода показано, как сделать извлекать метрики при использовании метода [AsDocument'ery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx)

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
## <a name="aggregating-querymetrics"></a>Агрегирование queryMetrics

В предыдущем разделе обратите внимание, что было несколько вызовов метода [ExecuteNextAsync.](https://msdn.microsoft.com/library/azure/dn850294.aspx) Каждый вызов `FeedResponse` возвращал объект, в `QueryMetrics`который есть словарь; один для каждого продолжения запроса. В следующем примере показано, как их агрегировать `QueryMetrics` с помощью LIN'а:

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

## <a name="grouping-query-metrics-by-partition-id"></a>Показатели группового запроса по идентификатору раздела

Вы можете `QueryMetrics` сгруппировать идентификатор раздела. Группировка по идентификатору раздела позволяет увидеть, вызывает ли определенный раздел проблемы с производительностью по сравнению с другими. В следующем примере `QueryMetrics` показано, как сгруппироваться с ЛИНЗ:

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

## <a name="linq-on-documentquery"></a>ЛИНЗ на ДокументеКери

Вы также можете `FeedResponse` получить от запроса `AsDocumentQuery()` LIN's, используя метод:

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

Можно захватить единицы запроса, потребляемые каждым запросом, для исследования дорогих запросов или запросов, потребляемых высокой пропускной однако. Вы можете получить плату за запрос, `FeedResponse`используя свойство [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) в . Подробнее о том, как получить плату за запрос, можно узнать с помощью портала Azure и различных SDK, смотрите статью [о взимании платы за единицу запроса.](find-request-unit-charge.md)

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

## <a name="get-the-query-execution-time"></a>Получите время выполнения запроса

При расчете времени, необходимого для выполнения запроса на стороне клиента, убедитесь, что вы включаете только время вызова `ExecuteNextAsync` метода, а не другие части вашей базы кода. Именно эти вызовы помогут вам вычислить, сколько времени занимает выполнение запроса, как показано в следующем примере:

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>Запросы сканирования (обычно медленные и дорогие)

Запрос сканирования относится к запросу, который не был обслужен индексом, из-за которого многие документы загружаются перед возвратом набора результатов.

Ниже приведен пример запроса сканирования:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Этот фильтр запроса использует функцию системы UPPER, которая не подается из индекса. Выполнение этого запроса в отношении большой коллекции привело к следующим показателям запроса для первого продолжения:

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

Обратите внимание на следующие значения из вывода метрик запроса:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Этот запрос загрузил 60 951 документ, что составило 399 998 938 байтов. Загрузка этого количества байтов приводит к высокой стоимости или сбору единицы запроса. Выполнение запроса занимает много времени, что ясно с общим временем, затраченным на свойство:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Это означает, что выполнение запроса заняло 4,5 секунды (и это было только одно продолжение).

Чтобы оптимизировать этот пример запроса, избегайте использования UPPER в фильтре. Вместо этого, когда документы `c.description` создаются или обновляются, значения должны быть вставлены во все символы верхнего регистра. Затем запрос становится: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Теперь этот запрос может быть подан из индекса.

Чтобы узнать больше о производительности тюнинга запроса, смотрите статью [Tune Query Performance.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="references"></a><a id="References"></a>Ссылки

- [DocumentDB SQL Syntax](https://go.microsoft.com/fwlink/p/?LinkID=510612) (Синтаксис SQL в DocumentDB)
- [АНСИ СЗЛ 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [Json](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка производительности запросов](sql-api-query-metrics.md)
- [Общие сведения об индексировании](index-overview.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
