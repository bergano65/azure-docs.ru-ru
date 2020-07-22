---
title: Настройка нескольких источников в Azure Cosmos DB
description: Узнайте, как настроить несколько хозяев для приложений с помощью разных пакетов SDK в Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/02/2019
ms.author: mjbrown
ms.custom: tracking-python
ms.openlocfilehash: a07ab3f65fcdeacf37626ad05d0b95ac3f4e7e64
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263674"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Настройка нескольких источников в приложениях, использующих Azure Cosmos DB

После создания учетной записи с несколькими включенными регионами записи необходимо внести два изменения в приложении в ConnectionPolicy для DocumentClient, чтобы включить возможности нескольких источников и множественной адресации в Azure Cosmos DB. В ConnectionPolicy установите для UseMultipleWriteLocations значение true и передайте имя региона, в котором развернуто приложение, в SetCurrentLocation. Это заполнит свойство PreferredLocations на основе географической близости от передаваемого расположения. Позже при добавлении нового региона в учетную запись нет необходимости обновлять или повторно развертывать приложение. В случае инициации регионального события оно автоматически обнаружит ближайший регион и подключится к нему.

> [!Note]
> Учетные записи Cosmos, изначально настроенные для записи в один регион, можно без какого-либо простоя настроить для записи в несколько регионов (т. е. для использования архитектуры "несколько источников"). Дополнительные сведения см. в разделе [Настройка нескольких регионов записи](how-to-manage-database-account.md#configure-multiple-write-regions).

## <a name="net-sdk-v2"></a><a id="netv2"></a>Пакет SDK для .NET версии 2

Чтобы включить несколько источников в приложении, задайте для параметра `UseMultipleWriteLocations` значение `true`. Кроме того, задайте для `SetCurrentLocation` регион, в котором развернуто приложение и реплицируется Azure Cosmos DB:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>Пакет SDK версии 3 для .NET

Чтобы включить несколько источников в своем приложении, задайте для параметра `ApplicationRegion` регион, в котором развернуто приложение и реплицируется Cosmos DB:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

При необходимости можно использовать `CosmosClientBuilder` и `WithApplicationRegion` для достижения того же результата:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-master"></a> Пакет SDK для Java версии 4

Чтобы включить несколько хозяев в приложении, вызовите `.multipleWriteRegionsEnabled(true)` и `.preferredRegions(preferredRegions)` в построителе клиентов, где — это элемент, `preferredRegions` `List` содержащий один элемент, то есть регион, в котором развертывается приложение и где Cosmos DB реплицируется:

# <a name="async"></a>[Асинхронный режим](#tab/api-async)

   Асинхронный API [пакета SDK для Java версии 4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos))

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Синхронизация](#tab/api-sync)

   Синхронный API [пакета SDK для Java версии 4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos))

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-milti-master"></a>Асинхронный пакет SDK для Java v2

Пакет SDK для Java v2 использовал Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Чтобы включить несколько источников в своем приложении, настройте параметр `policy.setUsingMultipleWriteLocations(true)` и задайте для параметра `policy.setPreferredLocations` регион, в котором развертывается приложение и реплицируется Cosmos DB:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Пакеты SDK для Node.js, JavaScript и TypeScript

Чтобы включить несколько источников в приложении, задайте для параметра `connectionPolicy.UseMultipleWriteLocations` значение `true`. Кроме того, задайте для параметра `connectionPolicy.PreferredLocations` регион, в котором развернуто приложение и реплицируется Cosmos DB:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Пакет SDK для Python

Чтобы включить несколько источников в приложении, задайте для параметра `connection_policy.UseMultipleWriteLocations` значение `true`. Кроме того, задайте для параметра `connection_policy.PreferredLocations` регион, в котором развернуто приложение и реплицируется Cosmos DB.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Дальнейшие шаги

Ознакомьтесь со следующими статьями:

* [Использование маркеров сеанса](how-to-manage-consistency.md#utilize-session-tokens)
* [Типы конфликтов и политики их устранения](conflict-resolution-policies.md)
* [Высокий уровень доступности в Azure Cosmos DB](high-availability.md)
* [Уровни согласованности в Azure Cosmos DB](consistency-levels.md)
* [Choose the right consistency level](consistency-levels-choosing.md) (Выбор правильного уровня согласованности)
* [Недостатки согласованности, доступности и производительности в Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Достижение компромисса между доступностью и быстродействием для разных уровней согласованности](consistency-levels-tradeoffs.md)
* [Глобальное масштабирование подготовленной пропускной способности](scaling-throughput.md)
* [Глобальное распределение. внутри](global-dist-under-the-hood.md)
