---
title: Выполнение запроса SQL в Azure Cosmos DB
description: Дополнительные сведения о выполнении запросов SQL в Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: e4e26b658bd29e4589be40e4d29935059836c909
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342866"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Выполнение запроса Azure Cosmos DB SQL

Любой язык, позволяющего отправлять запросы HTTP/HTTPS можно вызвать Cosmos DB REST API. Кроме того, Cosmos DB предлагает программные библиотеки для языков программирования .NET, Node.js, JavaScript и Python. REST API и библиотеки поддерживают запросы через SQL и пакета SDK для .NET также поддерживает [запросов LINQ](sql-query-linq-to-sql.md).

Следующие примеры показывают, как создать запрос и выполнить его в учетной записи базы данных Cosmos DB.

## <a id="REST-API"></a>REST API

Cosmos DB предлагает простую и открытую модель программирования RESTful поверх HTTP. Модель ресурсов состоит из набора ресурсов под учетной записью базы данных, который подготавливает подписку Azure. Учетная запись базы данных состоит из набора *баз данных*, каждый из которых может содержать несколько *контейнеры*, который в свою очередь содержать *элементы*, определяемые пользователем функции и другие типы ресурсов. Каждый ресурс Cosmos DB доступен по логическому постоянному URI. Набор ресурсов называется *веб-канала*. 

Базовая модель взаимодействия с этими ресурсами осуществляется через команды HTTP `GET`, `PUT`, `POST`, и `DELETE`, с их стандартной интерпретации. Используйте `POST` для создания нового ресурса, выполнения хранимой процедуры, или выдать запрос Cosmos DB. Запросы всегда включают только операции чтения без побочных эффектов.

В приведенных ниже примерах `POST` API SQL для запроса к образцов элементов. Запрос имеет простой фильтр на JSON `name` свойство. `x-ms-documentdb-isquery` И Content-Type: `application/query+json` заголовки указывают, что операция является запросом. Замените `mysqlapicosmosdb.documents.azure.com:443` с URI для вашей учетной записи Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Получаются такие результаты:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

Далее, более сложный запрос возвращает несколько результатов из соединения:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

Получаются такие результаты: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Если результаты запроса не помещаются на одной странице, REST API возвращает токен продолжения в `x-ms-continuation-token` заголовок ответа. Клиенты могут разбивать результаты на страницы, включая заголовок в последующие результаты. Можно также управлять количество результатов на странице через `x-ms-max-item-count` заголовок номера.

Если запрос содержит статистическую функцию как число, на странице запроса могут возвращать частично агрегированное значение только одна страница результатов. Клиенты должны выполнить статистическую обработку второго уровня этих результатов, чтобы получить окончательные результаты. Например сумму чисел, возвращенных на отдельных страницах, для получения общего числа.

Для управления политикой согласованности данных для запросов, используйте `x-ms-consistency-level` заголовка, как и все запросы REST API. Согласованность уровня сеанса также требует, предоставляющим последнюю версию `x-ms-session-token` заголовок cookie в запросе. Политика индексации запрашиваемого контейнера также может повлиять на согласованность результатов запроса. По умолчанию, параметры политики для контейнеров индексирования, индекс всегда является текущее содержимое элемента, и результаты запроса соответствуют выбранным для данных. Дополнительные сведения см. в разделе [уровней согласованности Azure Cosmos DB] [уровней согласованности].

Если настроенная политика индексации в контейнере не поддерживает указанный запрос, сервер Azure Cosmos DB возвращает 400 «Bad Request». Это сообщение об ошибке возвращает для запросов с путями, явно исключенным из индексации. Можно указать `x-ms-documentdb-query-enable-scan` заголовок, чтобы разрешить запрос, чтобы выполнить проверку, если индекс недоступен.

Можно получить подробные метрики выполнения запроса, задав `x-ms-documentdb-populatequerymetrics` для заголовка `true`. Дополнительные сведения см. в статье [Tuning query performance with Azure Cosmos DB](sql-api-query-metrics.md) (Настройка производительности запросов с помощью Azure Cosmos DB).

## <a name="c-net-sdk"></a>C#(ПАКЕТ SDK ДЛЯ .NET)

Пакет .NET SDK поддерживает запросы как LINQ, так и SQL. В следующем примере показано, как выполнить предыдущий запрос фильтра с помощью .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

В следующем примере сравниваются два свойства для определения равенства внутри каждого элемента и использует анонимные проекции.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

В следующем примере показано объединение, выражаемое через LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

Клиент .NET автоматически перебирает все страницы результатов запроса в `foreach` блокирует, как показано в предыдущем примере. Параметры запроса появился в [REST API](#REST-API) раздел также доступны в пакете SDK .NET с помощью `FeedOptions` и `FeedResponse` классы в `CreateDocumentQuery` метод. Число страниц, которые можно контролировать с помощью `MaxItemCount` параметр.

Можно также явно управлять разбиение по страницам путем создания `IDocumentQueryable` с помощью `IQueryable` объекта, а затем считывая `ResponseContinuationToken` значения и передавая их обратно как `RequestContinuationToken` в `FeedOptions`. Можно задать `EnableScanInQuery` Включение сканирования, когда запрос не поддерживается службой настроенная политика индексации. Для секционированных контейнеров, можно использовать `PartitionKey` для выполнения запроса с одной секцией, несмотря на то, что Azure Cosmos DB может автоматически извлекать этот ключ из текста запроса. Можно использовать `EnableCrossPartitionQuery` для выполнения запросов к нескольким секциям.

Дополнительные примеры кода .NET с запросами, см. в разделе [примеры Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet) в GitHub.

## <a id="JavaScript-server-side-API"></a>Интерфейс API для серверного JavaScript

Azure Cosmos DB предоставляет модель программирования для [выполнять JavaScript приложения на основе](stored-procedures-triggers-udfs.md) логики напрямую в контейнерах, с помощью хранимых процедур и триггеров. Логика JavaScript регистрируется на уровне контейнера, может выполнять операции с базой данных для элементов заданного контейнера, в оболочку оборачиваются транзакциями ACID.

В следующем примере показано, как использовать `queryDocuments` на сервере JavaScript API, чтобы формировать запросы изнутри хранимых процедур и триггеров:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Дальнейшие действия

- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Настраиваемые уровни согласованности Azure Cosmos DB](consistency-levels.md)
