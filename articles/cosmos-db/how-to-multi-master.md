---
title: Настройка нескольких источников в Azure Cosmos DB
description: Сведения о том, как настроить несколько источников в приложениях в Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: b862c59002369662d37b6d6a9de28370b0000497
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682276"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Настройка нескольких источников в приложениях, использующих Azure Cosmos DB

Чтобы использовать несколько источников в своих приложениях, нужно включить выполнение операций записи в нескольких регионах и настроить множественную адресацию в Azure Cosmos DB. Для настройки множественной адресации нужно указать регион, в котором развертывается приложение.

## <a id="netv2"></a>Пакет SDK для .NET версии 2

Чтобы включить несколько источников в своем приложении, укажите для параметра `UseMultipleWriteLocations` значение true, а для параметра `SetCurrentLocation` — регион, в котором развертывается приложение и реплицируется Azure Cosmos DB.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>Пакет SDK для .NET версии 3 (предварительная версия)

Чтобы включить несколько источников в своем приложении, укажите для параметра `UseCurrentRegion` регион, в котором развернуто приложение и реплицируется Cosmos DB.

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Пакет SDK для Java (асинхронная модель)

Чтобы включить несколько источников в своем приложении, настройте параметр `policy.setUsingMultipleWriteLocations(true)` и укажите для параметра `policy.setPreferredLocations` регион, в котором развертывается приложение и реплицируется Cosmos DB.

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

## <a id="javascript"></a>Пакет SDK для Node.js, JavaScript и TypeScript

Чтобы включить несколько источников в своем приложении, укажите для параметра `connectionPolicy.UseMultipleWriteLocations` значение true, а для параметра `connectionPolicy.PreferredLocations` — регион, в котором развертывается приложение и реплицируется Cosmos DB.

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

## <a id="python"></a>Пакет SDK для Python

Чтобы включить несколько источников в своем приложении, укажите для параметра `connection_policy.UseMultipleWriteLocations` значение true, а для параметра `connection_policy.PreferredLocations` — регион, в котором развертывается приложение и реплицируется Cosmos DB.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Дополнительная информация

См. подробнее:

* [Использование маркеров сеанса](how-to-manage-consistency.md#utilize-session-tokens)
* [Типы конфликтов и политики их устранения](conflict-resolution-policies.md)
* [Высокий уровень доступности при использовании Azure Cosmos DB](high-availability.md)
* [Настраиваемые уровни согласованности данных в Azure Cosmos DB](consistency-levels.md)
* [Выбор правильного уровня согласованности](consistency-levels-choosing.md)
* [Компромиссы согласованности, доступности и производительности](consistency-levels-tradeoffs.md)
* [Достижение компромисса между доступностью и быстродействием для разных уровней согласованности](consistency-levels-tradeoffs.md)
* [Масштабирование пропускной способности в Azure Cosmos DB](scaling-throughput.md)
* [Глобальное распределение (взгляд изнутри)](global-dist-under-the-hood.md)
