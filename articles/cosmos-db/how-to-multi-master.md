---
title: Настройка нескольких источников в Azure Cosmos DB
description: Узнайте, как настроить мультимастер для приложений, используя различные SDK в Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873647"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Настройка нескольких источников в приложениях, использующих Azure Cosmos DB

После создания учетной записи с несколькими включенными регионами записи необходимо внести два изменения в приложении в ConnectionPolicy для DocumentClient, чтобы включить возможности нескольких источников и множественной адресации в Azure Cosmos DB. В ConnectionPolicy установите для UseMultipleWriteLocations значение true и передайте имя региона, в котором развернуто приложение, в SetCurrentLocation. Это заполнит свойство PreferredLocations на основе географической близости от передаваемого расположения. Позже при добавлении нового региона в учетную запись нет необходимости обновлять или повторно развертывать приложение. В случае инициации регионального события оно автоматически обнаружит ближайший регион и подключится к нему.

> [!Note]
> Учетные записи Cosmos, изначально настроенные для записи в один регион, можно без какого-либо простоя настроить для записи в несколько регионов (т. е. для использования архитектуры "несколько источников"). Дополнительные сведения см. в разделе [Настройка нескольких регионов записи](how-to-manage-database-account.md#configure-multiple-write-regions).

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

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

## <a name="net-sdk-v3"></a><a id="netv3"></a>Пакет SDK для .NET версии 3

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

## <a name="java-async-sdk"></a><a id="java"></a>Пакет SDK для Java (асинхронная модель)

Чтобы включить несколько источников в своем приложении, настройте параметр `policy.setUsingMultipleWriteLocations(true)` и задайте для параметра `policy.setPreferredLocations` регион, в котором развертывается приложение и реплицируется Cosmos DB:

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

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь со следующими статьями:

* [Использование маркеров сеанса](how-to-manage-consistency.md#utilize-session-tokens)
* [Типы конфликтов и политики их устранения](conflict-resolution-policies.md)
* [Высокая доступность в Azure Cosmos DB](high-availability.md)
* [Уровни согласованности в Azure Cosmos DB](consistency-levels.md)
* [Choose the right consistency level](consistency-levels-choosing.md) (Выбор правильного уровня согласованности)
* [Согласованность, доступность и производительность в Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Достижение компромисса между доступностью и быстродействием для разных уровней согласованности](consistency-levels-tradeoffs.md)
* [Глобальное масштабирование подготовленной пропускной способности](scaling-throughput.md)
* [Глобальное распространение: под капотом](global-dist-under-the-hood.md)
