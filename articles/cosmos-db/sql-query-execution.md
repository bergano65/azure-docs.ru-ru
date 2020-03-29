---
title: Выполнение запроса в Azure Cosmos DB
description: Узнайте, как создать запрос s'L и выполнить его в Azure Cosmos DB. В этой статье описывается, как создавать и выполнять запрос с помощью REST API, .Net SDK, JavaScript SDK и различных других SDK.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871267"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Выполнение запроса Azure Cosmos DB S'L

Любой язык, способный сделать запросы HTTP/HTTPS, может вызывать Сяочко DB REST API. Cosmos DB также предлагает библиотеки программирования для языков программирования .NET, Node.js, JavaScript и Python. REST API и библиотеки поддерживают запросы через S'L, а .NET SDK также поддерживает [запрос LIN'](sql-query-linq-to-sql.md).

Ниже приведены следующие примеры, как создать запрос и отправить его в учетную запись базы данных Cosmos.

## <a name="rest-api"></a><a id="REST-API"></a>REST API

Cosmos DB предлагает простую и открытую модель программирования RESTful поверх HTTP. Модель ресурса состоит из набора ресурсов в базе данных, которые предусмотрены подпиской Azure. Учетная запись базы данных состоит из набора *баз данных,* каждая из которых может содержать несколько *контейнеров,* которые, в свою очередь, содержат *элементы,* UDF и другие типы ресурсов. Каждый ресурс Cosmos DB можно адресовать с помощью логического и стабильного URI. Набор ресурсов называется *кормом.* 

Основная модель взаимодействия с этими ресурсами `GET` `PUT`через `POST`глаголы HTTP , , , и `DELETE`, с их стандартными интерпретациями. Используется `POST` для создания нового ресурса, выполнения сохраненной процедуры или выдачи запроса Cosmos DB. Запросы всегда включают только операции чтения без побочных эффектов.

Следующие примеры `POST` отображаем запрос API S'L против элементов выборки. Запрос имеет простой фильтр на `name` свойстве JSON. И `x-ms-documentdb-isquery` тип содержимого: `application/query+json` заголовки означают, что операция является запросом. Замените `mysqlapicosmosdb.documents.azure.com:443` URI для учетной записи Cosmos DB.

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

Результаты:

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

Следующий, более сложный запрос возвращает несколько результатов от соединения:

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

Результаты: 

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

Если результаты запроса не могут поместиться в одну страницу, API REST `x-ms-continuation-token` возвращает токен продолжения через заголовок ответа. Клиенты могут paginate результаты, включив заголовок в последующие результаты. Вы также можете контролировать количество результатов `x-ms-max-item-count` на странице через заголовок номера.

Если запрос имеет функцию агрегирования, например COUNT, страница запроса может вернуть частично агрегированное значение только на одну страницу результатов. Клиенты должны выполнить агрегацию второго уровня по этим результатам, чтобы дать окончательные результаты. Например, сумма по счетам, возвращенным на отдельные страницы, чтобы вернуть общий счет.

Для управления политикой согласованности данных для `x-ms-consistency-level` запросов используйте заголовок, как и во всех запросах REST API. Последовательность сеанса также требует `x-ms-session-token` отражения последнего заголовка cookie в запросе запроса запроса. Политика индексации запрашиваемого контейнера также может повлиять на согласованность результатов запроса. При настройках политики индексирования по умолчанию для контейнеров индекс всегда актуается с содержимым элемента, а результаты запроса соответствуют согласованности, выбранной для данных. Для получения дополнительной информации см.

Если настроенная политика индексирования в контейнере не может поддерживать указанный запрос, сервер Azure Cosmos DB возвращает 400 "Плохой запрос". Это сообщение об ошибке возвращается для запросов с путями, явно исключенными из индексации. Можно указать `x-ms-documentdb-query-enable-scan` заголовок, чтобы запрос мог выполнить сканирование, когда индекс недоступен.

Вы можете получить подробные метрики `x-ms-documentdb-populatequerymetrics` по `true`исполнению запроса, установив заголовок. Дополнительные сведения см. в статье [Tuning query performance with Azure Cosmos DB](sql-api-query-metrics.md) (Настройка производительности запросов с помощью Azure Cosmos DB).

## <a name="c-net-sdk"></a>СЗ (.NET SDK)

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

В следующем примере сравниваются два свойства для равенства внутри каждого элемента и используются анонимные проекции.

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

Следующий пример показывает соединения, выраженные через LIN ' `SelectMany`.

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

Клиент .NET автоматически итерирует все страницы запроса `foreach` в блоках, как показано в предыдущем примере. Параметры запроса, представленные в разделе [REST API,](#REST-API) также доступны в `FeedOptions` `FeedResponse` .NET `CreateDocumentQuery` SDK, используя и классы в методе. Вы можете управлять количеством страниц `MaxItemCount` с помощью параметра.

Вы также можете явно контролировать `IDocumentQueryable` paging, создавая `IQueryable` с `ResponseContinuationToken` помощью объекта, `RequestContinuationToken` а `FeedOptions`затем читая значения и передавая их обратно, как в . Можно настроить `EnableScanInQuery` сканирование, когда запрос не поддерживается настроенной политикой индексации. Для разделенных контейнеров можно использовать `PartitionKey` для выполнения запроса против одного раздела, хотя Azure Cosmos DB может автоматически извлечь это из текста запроса. Можно использовать `EnableCrossPartitionQuery` для выполнения запросов против нескольких разделов.

Дополнительные примеры .NET с запросами можно узнать в GitHub [образцах Azure Cosmos DB .NET.](https://github.com/Azure/azure-cosmos-dotnet-v3)

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>Интерфейс API для серверного JavaScript

Azure Cosmos DB предоставляет модель программирования для [выполнения логики приложения на основе JavaScript](stored-procedures-triggers-udfs.md) непосредственно на контейнерах, используя сохраненные процедуры и триггеры. Логика JavaScript, зарегистрированная на уровне контейнеров, может выдавать операции базы данных по элементам данного контейнера, завернутые в операции ambient ACID.

Ниже приводится следующий `queryDocuments` пример, как использовать aPI сервера JavaScript для выполнения запросов из сохраненных процедур и триггеров:

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

- [Введение в Azure Космос DB](introduction.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Уровни согласованности Azure Cosmos DB](consistency-levels.md)
