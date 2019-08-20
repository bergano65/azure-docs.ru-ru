---
title: Выполнение SQL запроса в Azure Cosmos DB
description: Сведения о выполнении запросов SQL в Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: 0eca458c344e5c44ad62121db14e6b286dc19a86
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614439"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB выполнения запросов SQL

Любой язык, способный выполнять запросы HTTP/HTTPS, может вызывать REST API Cosmos DB. Cosmos DB также предлагает библиотеки программирования для языков программирования .NET, Node. js, JavaScript и Python. REST API и библиотеки поддерживают запросы с помощью SQL, а пакет SDK для .NET также поддерживает [запросы LINQ](sql-query-linq-to-sql.md).

В следующих примерах показано, как создать запрос и отправить его в учетную запись базы данных Cosmos.

## <a id="REST-API"></a>REST API

Cosmos DB предлагает простую и открытую модель программирования RESTful поверх HTTP. Модель ресурсов состоит из набора ресурсов в учетной записи базы данных, которую подготавливает подписка Azure. Учетная запись базы данных состоит из набора *баз*данных, каждый из которых может содержать несколько *контейнеров*, которые, в свою очередь, содержат *элементы*, UDF и другие типы ресурсов. Каждый ресурс Cosmos DB является адресацией с помощью логического и стабильного URI. Набор ресурсов называется *веб*-каналом. 

Основная модель взаимодействия с этими ресурсами заключается в использовании `GET`глаголов HTTP `POST`, `PUT`, и `DELETE`с их стандартной интерпретацией. Используйте `POST` для создания нового ресурса, выполнения хранимой процедуры или запроса Cosmos DB. Запросы всегда включают только операции чтения без побочных эффектов.

В следующих примерах показан `POST` запрос API SQL к примерам элементов. Запрос имеет простой фильтр для свойства JSON `name` . Типы и Content-Type: `application/query+json` заголовки обозначают, что операция является запросом. `x-ms-documentdb-isquery` Замените `mysqlapicosmosdb.documents.azure.com:443` на универсальный код ресурса (URI) для учетной записи Cosmos DB.

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

Следующий, более сложный запрос возвращает несколько результатов из объединения:

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

Если результаты запроса не помещаются на одной странице, REST API возвращает маркер продолжения через `x-ms-continuation-token` заголовок ответа. Клиенты могут разбивать результаты на страницы, включая заголовок в последующих результатах. Можно также управлять количеством результатов на страницу с помощью `x-ms-max-item-count` заголовка Number.

Если запрос содержит статистическую функцию, например COUNT, то страница запроса может вернуть частично агрегированное значение только на одной странице результатов. Для получения окончательных результатов клиенты должны выполнить статистическую обработку второго уровня по этим результатам. Например, можно суммировать значения счетчиков, возвращаемых отдельными страницами, чтобы получить общее число.

Чтобы управлять политикой согласованности данных для запросов, используйте `x-ms-consistency-level` заголовок, как во всех запросах REST API. Для обеспечения согласованности сеансов также требуется выводить в запросе новый `x-ms-session-token` заголовок файла cookie. Политика индексации запрашиваемого контейнера также может повлиять на согласованность результатов запроса. При использовании параметров политики индексирования по умолчанию для контейнеров индекс всегда является текущим содержимым элемента, а результаты запроса соответствуют согласованности, выбранной для данных. Дополнительные сведения см. в разделе [уровни согласованности Azure Cosmos DB] [уровни согласованности].

Если настроенная политика индексирования контейнера не поддерживает указанный запрос, Azure Cosmos DB сервер возвращает 400 "Недопустимый запрос". Это сообщение об ошибке возвращается для запросов с путями, явно исключенными из индексирования. Можно указать `x-ms-documentdb-query-enable-scan` заголовок, позволяющий запросу выполнять проверку, если индекс недоступен.

Подробные метрики для выполнения запроса можно получить, задав `x-ms-documentdb-populatequerymetrics` для `true`заголовка значение. Дополнительные сведения см. в статье [Tuning query performance with Azure Cosmos DB](sql-api-query-metrics.md) (Настройка производительности запросов с помощью Azure Cosmos DB).

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

В следующем примере сравниваются два свойства на равенство внутри каждого элемента и используются анонимные проекции.

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

В следующем примере показаны объединения, выраженные через LINQ `SelectMany`.

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

Клиент .NET автоматически проходит по всем страницам результатов запроса в `foreach` блоках, как показано в предыдущем примере. Параметры запроса, представленные в разделе [REST API](#REST-API) , также доступны в пакете SDK для .NET с использованием `FeedOptions` классов `FeedResponse` `CreateDocumentQuery` и в методе. С помощью `MaxItemCount` параметра можно управлять количеством страниц.

Можно также явно управлять разбиением на страницы `IDocumentQueryable` , создав `IQueryable` с помощью объекта `ResponseContinuationToken` , прочитав значения и передав их обратно `RequestContinuationToken` в `FeedOptions`. Можно задать `EnableScanInQuery` Включение проверок, если запрос не поддерживается настроенной политикой индексирования. Для секционированных контейнеров можно использовать `PartitionKey` для выполнения запроса к одной секции, хотя Azure Cosmos DB может автоматически извлекать его из текста запроса. Можно использовать `EnableCrossPartitionQuery` для выполнения запросов к нескольким секциям.

Дополнительные примеры .NET с запросами см. в разделе [примеры Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet) на сайте GitHub.

## <a id="JavaScript-server-side-API"></a>Интерфейс API для серверного JavaScript

Azure Cosmos DB предоставляет модель программирования для запуска логики [приложения на основе JavaScript](stored-procedures-triggers-udfs.md) непосредственно в контейнерах с помощью хранимых процедур и триггеров. Логика JavaScript, зарегистрированная на уровне контейнера, может затем выдавать операции базы данных для элементов данного контейнера, упакованных в окружающие транзакции ACID.

В следующем примере показано, как использовать `queryDocuments` в API сервера JavaScript для выполнения запросов внутри хранимых процедур и триггеров:

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

## <a name="next-steps"></a>Следующие шаги

- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Уровни согласованности Azure Cosmos DB](consistency-levels.md)
