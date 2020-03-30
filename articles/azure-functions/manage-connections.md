---
title: Управление соединениями в функциях Azure
description: Узнайте, как избежать проблем с производительностью в службе "Функции Azure" с помощью статического подключения клиентов.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276456"
---
# <a name="manage-connections-in-azure-functions"></a>Управление соединениями в функциях Azure

Функции в функциональном приложении делятся ресурсами. Среди этих общих ресурсов есть соединения: http-соединения, соединения баз данных и подключения к службам, таким как Хранилище Azure. При параллельном выполнении многих функций можно остаться без доступных подключений. В этой статье объясняется, как кодировать свои функции, чтобы избежать использования большего количества подключений, чем им нужно.

## <a name="connection-limit"></a>Ограничение соединения

Количество доступных подключений ограничено отчасти потому, что функциональное приложение работает в [среде песочницы.](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) Одним из ограничений, которые песочница накладывает на ваш код, является ограничение на количество исходящих соединений, которое в настоящее время составляет 600 активных (1200 всего) соединений на экземпляр. Когда вы достигнете этого предела, время выполнения функций `Host thresholds exceeded: Connections`записывает следующее сообщение в журналы: . Для получения дополнительной [информации](functions-scale.md#service-limits)см.

Это ограничение в каждом экземпляре. Когда [контроллер масштаба добавляет экземпляры приложения функции](functions-scale.md#how-the-consumption-and-premium-plans-work) для обработки большего количества запросов, каждый экземпляр имеет независимый предел соединения. Это означает, что глобального ограничения соединения нет, и вы можете иметь гораздо более 600 активных соединений во всех активных экземплярах.

При устранении неполадок убедитесь, что вы включили приложение Application Insights для приложения функции. Application Insights позволяет просматривать метрики для функциональных приложений, таких как исполнения. Для получения дополнительной информации смотрите [телеметрию в Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Статические клиенты

Чтобы избежать большего количества подключений, чем необходимо, повторно используйте экземпляры клиента, а не создавайте новые с каждым вызовом функции. Мы рекомендуем повторно использовать клиентские соединения для любого языка, на котором вы можете написать свою функцию. Например, клиенты .NET, такие как [клиенты HttpClient,](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)и Azure Storage, могут управлять соединениями, если вы используете одного статического клиента.

Ниже приведены некоторые рекомендации, которые следует соблюдать при использовании клиента, работаемого в приложении Azure Functions:

- *Не создавайте* нового клиента с каждой функцией вызова.
- *Создайте* единый статический клиент, который может использовать каждая функция вызова.
- *Рассмотрите возможность* создания единого статического клиента в общем классе помощников, если разные функции используют одну и ту же службу.

## <a name="client-code-examples"></a>Примеры кода клиента

В этом разделе даются рекомендации по созданию и использованию клиентов из вашего кода функции.

### <a name="httpclient-example-c"></a>Пример HttpClient (C#)

Вот пример кода функции C, который создает статический экземпляр [httpclient:](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Общий вопрос о [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) в .NET: "Должен ли я избавиться от моего клиента?" Как правило, вы распоряжаетесь объектами, которые реализуются, `IDisposable` когда вы закончите использовать их. Но вы не распоряжаться статического клиента, потому что вы не сделали, используя его, когда функция заканчивается. Необходимо, чтобы статический клиент существовал в течение срока жизни приложения.

### <a name="http-agent-examples-javascript"></a>Примеры агентов HTTP (JavaScript)

Поскольку он обеспечивает лучшие варианты управления [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) подключением, следует использовать родной `node-fetch` класс вместо неродных методов, таких как модуль. Параметры соединения настраиваются на параметры `http.agent` в классе. Для подробных опций, доступных с агентом HTTP, [см. новый агент (варианты\[\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Глобальный `http.globalAgent` класс, `http.request()` используемый, имеет все эти значения, установленные для соответствующих по умолчанию. Чтобы настроить ограничения для подключений в Функциях Azure, рекомендуется глобально задать максимальное число. В следующем примере задается максимальное количество сокетов для приложения-функции.

```js
http.globalAgent.maxSockets = 200;
```

 Следующий пример создает новый запрос HTTP с пользовательским агентом HTTP только для этого запроса:

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

### <a name="cosmosclient-code-example-javascript"></a>Пример кода CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) подключается к экземпляру Azure Cosmos DB. В документации Azure Cosmos DB рекомендуется [использовать отдельный клиент Azure Cosmos DB в течении всего жизненного цикла приложения](../cosmos-db/performance-tips.md#sdk-usage). В следующем примере показан один шаблон в функции, чтобы это делать.

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Подключения SqlClient

Ваш функциональный код может использовать провайдер рамочных данных .NET для сервера S'L[(SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)для подключения к реляционной базе данных S'L. Это также основной поставщик для платформ данных, которые полагаются на ADO.NET, таких как [Entity Framework.](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) В отличие от соединений [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) и [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) ADO.NET осуществляет объединение подключений в пул по умолчанию. Но поскольку у вас все еще могут кончиться соединения, следует оптимизировать соединения с базой данных. Дополнительные сведения см. в разделе [Пулы подключений SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Некоторые платформы данных, такие как Entity Framework, обычно получают строки соединения из раздела **ConnectionStrings** файла конфигурации. В этом случае необходимо добавить строки подключений базы данных SQL непосредственно в список функциональных настроек приложения **Строки подключения** и в [файл local.settings.json](functions-run-local.md#local-settings-file) в локальном проекте. Если вы создаете экземпляр [SlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) в коде функции, необходимо хранить значение строки соединения в настройках приложения с другими **соединениями.**

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о том, почему мы рекомендуем статических клиентов, см [Неправильное мгновенное антипаттерн](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Дополнительные советы по повышению производительности службы "Функции Azure" см. в статье [Оптимизация производительности и надежности Функций Azure](functions-best-practices.md).
