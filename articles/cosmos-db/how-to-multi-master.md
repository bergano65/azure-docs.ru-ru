---
title: Настройка нескольких источников в Azure Cosmos DB
description: Сведения о том, как настроить несколько источников в приложениях в Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 1d9fa7380f62165d360888fd8cb03919f1736297
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244748"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Настройка нескольких источников в приложениях, использующих Azure Cosmos DB

Чтобы использовать несколько источников в приложении, нужно включить выполнение операций записи в нескольких регионах и настроить множественную адресацию в Azure Cosmos DB. Чтобы настроить множественную адресацию, укажите регион, в котором развертывается приложение.

## <a id="netv2"></a>Пакет SDK для .NET версии 2

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

## <a id="netv3"></a>Пакет SDK для .NET версии 3 (предварительная версия)

Чтобы включить несколько источников в своем приложении, задайте для параметра `UseCurrentRegion` регион, в котором развернуто приложение и реплицируется Cosmos DB:

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Пакет SDK для Java (асинхронная модель)

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

## <a id="javascript"></a>Пакеты SDK для Node.js, JavaScript и TypeScript

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

## <a id="python"></a>Пакет SDK для Python

Чтобы включить несколько источников в приложении, задайте для параметра `connection_policy.UseMultipleWriteLocations` значение `true`. Кроме того, задайте для параметра `connection_policy.PreferredLocations` регион, в котором развернуто приложение и реплицируется Cosmos DB.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь со следующими статьями:

* [Использование маркеров сеанса](how-to-manage-consistency.md#utilize-session-tokens)
* [Типы конфликтов и политики их устранения](conflict-resolution-policies.md)
* [Высокий уровень доступности при использовании Azure Cosmos DB](high-availability.md)
* [Настраиваемые уровни согласованности данных в Azure Cosmos DB](consistency-levels.md)
* [Choose the right consistency level](consistency-levels-choosing.md) (Выбор правильного уровня согласованности)
* [Компромиссы согласованности, доступности и производительности](consistency-levels-tradeoffs.md)
* [Достижение компромисса между доступностью и быстродействием для разных уровней согласованности](consistency-levels-tradeoffs.md)
* [Масштабирование пропускной способности в Azure Cosmos DB](scaling-throughput.md)
* [Глобальное распределение: работы Azure RMS](global-dist-under-the-hood.md)
