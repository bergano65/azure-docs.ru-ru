---
title: Управление подключениями в функциях Azure
description: Узнайте, как избежать проблем с производительностью в службе "Функции Azure" с помощью статического подключения клиентов.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 965fa1e82be3fb87bf58a0114f97091bad212738
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450742"
---
# <a name="manage-connections-in-azure-functions"></a>Управление подключениями в функциях Azure

Функции в приложении-функции совместно используют ресурсы. Среди этих ресурсов приведены подключений. Подключения по протоколу HTTP, подключения к базе данных и подключения к службам, например службу хранилища Azure. При параллельном выполнении многих функций можно остаться без доступных подключений. В этой статье описывается программирование функций можно отказаться от больше подключений, чем требуется.

## <a name="connection-limit"></a>Ограничение числа подключений

Количество доступных подключений ограничено, отчасти потому, что приложение-функция работает [среде-песочнице](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Одно из ограничений, которые "песочницы" налагает на ваш код является [ограничение на количество подключений (в настоящее время на 600 активных подключений и общее число подключений, 1200)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits) каждого экземпляра. По достижении этого ограничения среда выполнения функций создает журнал со следующим сообщением: `Host thresholds exceeded: Connections`.

Это ограничение не на один экземпляр.  Когда [контроллер масштабирования добавляет экземпляров приложения-функции](functions-scale.md#how-the-consumption-plan-works) возможность обрабатывать больше запросов, каждый экземпляр имеет ограничение независимое подключение. Это означает, что не ограничено глобальных подключений, и может иметь гораздо более чем 600 активных подключений для всех активных экземпляров.

## <a name="static-clients"></a>Статических клиентов

Чтобы избежать большего количества подключений, чем необходимо, повторно используйте экземпляры клиента, а не создавайте новые с каждым вызовом функции. Мы рекомендуем, повторно используя подключения клиента для любого языка, который может писать код функции на. Например, клиенты .NET, такие как [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), и клиентов хранилища Azure можно управлять подключениями, при использовании одного, статические клиента.

Ниже приведены некоторые рекомендации по вы используете клиент определенных служб в приложении "функции Azure".

- *Не* Создание нового клиента с помощью каждого вызова функции.
- *Сделать* создать единый, статические клиент, каждый вызов функции можно использовать.
- *Рассмотрите возможность* Создание единой, статические клиента в общих вспомогательный класс, если различные функции использовать ту же службу.

## <a name="client-code-examples"></a>Примеры кода клиента

В этом разделе даются рекомендации по созданию и использованию клиентов из вашего кода функции.

### <a name="httpclient-example-c"></a>Пример HttpClient (C#)

Ниже приведен пример C# код, который создает статический функции [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) экземпляр:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Часто задаваемые вопросы о [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) в .NET — «Следует ли освободить мой клиент?» Как правило, реализации объектов, реализующих `IDisposable` после завершения их использования. Но не удаляет из статического клиента, так как вы не сделали его использования, когда функция заканчивается. Необходимо, чтобы статический клиент существовал в течение срока жизни приложения.

### <a name="http-agent-examples-nodejs"></a>Примеры HTTP-агента (Node.js)

Так как собственный класс [ `http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) предоставляет лучшие возможности управления подключениями, следует использовать его вместо методов, не являющихся собственными, таких как модуль `node-fetch`. Параметры подключения настраиваются через параметры на `http.agent` класса. Подробные параметры, доступные с агентом HTTP, см. в разделе [новый агент (\[параметры\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Глобальный `http.globalAgent` класс, используемый `http.request()` имеет все эти значения, соответствующие значения по умолчанию. Чтобы настроить ограничения для подключений в Функциях Azure, рекомендуется глобально задать максимальное число. В следующем примере задается максимальное количество сокетов для приложения-функции.

```js
http.globalAgent.maxSockets = 200;
```

 В следующем примере создается новый запрос HTTP с помощью пользовательского агента HTTP только для этого запроса:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Пример кода DocumentClient (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) подключается к экземпляру Azure Cosmos DB. В документации Azure Cosmos DB рекомендуется [использовать отдельный клиент Azure Cosmos DB в течении всего жизненного цикла приложения](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). В следующем примере показан один шаблон в функции, чтобы это делать.

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

## <a name="sqlclient-connections"></a>Подключения SqlClient

Код функции можно использовать поставщик данных .NET Framework для SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) для подключения к реляционной базе данных SQL. Это также основной поставщик для платформы данным, которые основаны на ADO.NET, например [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). В отличие от соединений [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) и [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) ADO.NET осуществляет объединение подключений в пул по умолчанию. Но так как по-прежнему могут закончиться подключения, следует оптимизировать подключения к базе данных. Дополнительные сведения см. в разделе [Объединение подключений в пул в SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Некоторые платформы данных, например Entity Framework, обычно получают строк соединения из **ConnectionStrings** раздел файла конфигурации. В этом случае необходимо добавить строки подключений базы данных SQL непосредственно в список функциональных настроек приложения **Строки подключения** и в [файл local.settings.json](functions-run-local.md#local-settings-file) в локальном проекте. Если вы создаете экземпляр [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) в коде функции следует хранить значение строки подключения в **параметры приложения** с другими подключениями.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о причина статических клиентов, см. в разделе [неправильной установке](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Дополнительные советы по повышению производительности службы "Функции Azure" см. в статье [Оптимизация производительности и надежности Функций Azure](functions-best-practices.md).
