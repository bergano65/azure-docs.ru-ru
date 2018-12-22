---
title: Запрос контейнеров в Azure Cosmos DB
description: Узнайте, как запрашивать контейнеры в Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 11c68b61802f6c7b3755da71c176ea777f171e4c
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409842"
---
# <a name="query-containers-in-azure-cosmos-db"></a>Запрос контейнеров в Azure Cosmos DB

В этой статье описывается, как запрашивать контейнеры (коллекции, графы, таблицы) в Azure Cosmos DB.

## <a name="in-partition-query"></a>Запрос в секции

При запросе данных из контейнеров, если в запросе указан фильтр ключа секции, Azure Cosmos DB автоматически направляет запрос в секции, соответствующие значению ключа секции, указанному в фильтре. Например, следующий запрос направляется в секцию DeviceId, которая содержит все документы, соответствующие значению ключа секции XMS-0001.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Запрос между секциями

Следующий запрос не имеет фильтра ключа секции (DeviceId) и "прочесывает" все секции, где он выполняется с использованием индекса секции. Чтобы выполнить запрос по секциям, задайте для **EnableCrossPartitionQuery** значение true (или x-ms-documentdb-query-enablecrosspartition в REST API).

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB поддерживает агрегатные функции COUNT, MIN, MAX и AVG для работы с контейнерами с помощью SQL. Агрегатные функции для работы с контейнерами поддерживаются пакетами SDK версии 1.12.0 и выше. Запросы должны содержать один статистический оператор и одно значение в проекции.

## <a name="parallel-cross-partition-query"></a>Параллельный запрос между секциями

Пакеты SDK Cosmos DB 1.9.0 и более поздних версий поддерживают параметры выполнения параллельных запросов.  Параллельные запросы между секциями позволяют выполнять запросы с низкой задержкой. Например, следующий запрос настроен на выполнение параллельно по секциям.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Вы можете управлять параллельным выполнением запросов, регулируя следующие параметры.

- **MaxDegreeOfParallelism** — позволяет установить максимальное количество одновременных сетевых подключений к секциям контейнера. Если для параметра установить значение -1, степень параллелизма будет регулироваться пакетом SDK. Если параметр MaxDegreeOfParallelism не указан или имеет значение 0 (по умолчанию), то к секциям контейнера будет установлено одно сетевое подключение.

- **MaxBufferedItemCount** — задержка запросов на транзакции и использование памяти на стороне клиента. Если пропустить этот параметр или задать значение -1, число буферизованных элементов при параллельном выполнении запросов будет регулироваться пакетом SDK.

При неизменном состоянии коллекции параллельные запросы будут возвращать результаты в том же порядке, что и при последовательном выполнении. При выполнении межсекционного запроса, включающего в себя операторы сортировки (ORDER BY и/или TOP), пакет SDK для Azure Cosmos DB параллельно выполняет запрос между секциями и объединяет частично отсортированные результаты на стороне клиента для получения глобально упорядоченных результатов.

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать о секционировании в Cosmos DB, ознакомьтесь со следующими статьями:

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Секционирование в Azure Cosmos DB)
- [Create a synthetic partition key](synthetic-partition-keys.md) (Создание искусственного ключа секций)
