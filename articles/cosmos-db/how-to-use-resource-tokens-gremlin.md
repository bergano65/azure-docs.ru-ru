---
title: Маркеры ресурса Azure Cosmos DB с Gremlin
description: Сведения о создании маркеров ресурса и их использования для доступа к графовой базе данных
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70807015"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>Маркеры ресурса Azure Cosmos DB с Gremlin
В этой статье объясняется, как использовать [маркеры ресурса Cosmos DB](secure-access-to-data.md) для доступа к базе данных Graph через пакет SDK Gremlin.

## <a name="create-a-resource-token"></a>Создание маркера ресурса

Пакет SDK для TinkerPop Gremlin не имеет API для создания маркеров ресурса. Маркер ресурса является концепцией Cosmos DB. Для создания маркеров ресурса, скачайте [пакет SDK для Azure Cosmos DB](sql-api-sdk-dotnet.md). Если приложению необходимо создать маркеры ресурса и использовать их для доступа к базе данных Graph, потребуется 2 отдельных пакета SDK.

Иерархия объектной модели над маркерами ресурса:
- **Учетная запись Cosmos DB** — объект верхнего уровня, с которым связана служба доменных имен (DNS), например `contoso.gremlin.cosmos.azure.com`
  - **База данных Cosmos DB**
    - **User**
      - **Разрешение**
        - *Маркер* — свойство объекта **Разрешение**, определяющего, какие действия разрешены или запрещены.

Маркер ресурса имеет формат `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`. Эта строка является непрозрачной для клиентов и должна использоваться как есть без изменения или интерпретации.

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Использование маркера ресурса
Маркеры ресурса можно использовать непосредственно как свойство "пароль" при конструировании класса `GremlinServer`.

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Аналогичный подход работает во всех пакетах SDK для TinkerPop Gremlin.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Ограничение

Одна учетная запись Gremlin может выдавать неограниченное количество маркеров, однако одновременно можно использовать не более **100** маркеров в течение **1 часа**. Если приложение превышает ограничение на количество маркеров в час, запрос на аутентификацию будет отклонен с сообщением об ошибке `"Exceeded allowed resource token limit of 100 that can be used concurrently"`. Закрытие активных соединений с конкретными маркерами для освобождения слотов для новых маркеров не будет результативным. Ядро СУБД Cosmos DB Gremlin отслеживает отдельные маркеры за последний час до запроса на аутентификацию.

## <a name="permission"></a>Разрешение

Распространенные ошибки приложений, встречающиеся при использовании маркеров ресурса — это `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`. Эта ошибка возвращается, когда обход Gremlin пытается записать ребро или вершину, но маркер ресурса предоставляет `Read` только разрешения. Проверьте обход на содержание какого-либо из следующих шагов: `.addV()`, `.addE()`, `.drop()` или `.property()`.

## <a name="next-steps"></a>Дополнительная информация
* [Управление доступом на основе ролей](role-based-access-control.md) в Azure Cosmos DB
* [Сведения о защите доступа к данным](secure-access-to-data.md) в Azure Cosmos DB
