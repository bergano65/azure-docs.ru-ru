---
title: Запрос контейнеров в Azure Cosmos DB
description: Узнайте, как запрашивать контейнеры в Azure Cosmos DB с помощью запросов секционирования в разделах, перекрестных секций и частичных CORS.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 88ebb8bb80ec3406c98b77db481994d415b04373
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872032"
---
# <a name="query-an-azure-cosmos-container"></a>Запрос контейнера Azure Cosmos

В этой статье описывается, как запрашивать контейнеры (коллекции, графы и таблицы) в Azure Cosmos DB.

## <a name="in-partition-query"></a>Запрос в секции

Когда для получения данных из контейнеров отправляется запрос, в котором указан фильтр ключа секции, служба Azure Cosmos DB автоматически обрабатывает такой запрос. Эта служба направляет запрос в секции, соответствующие значениям ключей секций, указанным в фильтре. Например, следующий запрос направляется в секцию `DeviceId`, которая содержит все документы, соответствующие значению ключа секции `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Запрос между секциями

Следующий запрос не имеет фильтра ключа секции (`DeviceId`) и "прочесывает" все секции, где он выполняется с использованием индекса секции. Чтобы выполнить запрос ко всем секциям, задайте для `EnableCrossPartitionQuery` значение true (или `x-ms-documentdb-query-enablecrosspartition` в REST API).

Свойство EnablecrossPartitionQuery принимает логическое значение. Если задано значение true и запрос не содержит ключ секции, Azure Cosmos DB распространяет запрос по всем секциям. При таком распространении отдельные запросы направляются по всем секциям. Чтобы считать результаты запроса, клиентские приложения должны получить результаты из FeedResponse и проверить свойство ContinuationToken. Чтобы считывать все результаты, выполняйте итерацию данных, пока ContinuationToken не будет иметь значение null. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB поддерживает агрегатные функции COUNT, MIN, MAX и AVG для работы с контейнерами с помощью SQL. Агрегатные функции для работы с контейнерами поддерживаются пакетами SDK версии 1.12.0 и более поздней. Запросы должны содержать один статистический оператор и одно значение в проекции.

## <a name="parallel-cross-partition-query"></a>Параллельный запрос между секциями

Пакеты SDK Azure Cosmos DB 1.9.0 и более поздних версий поддерживают параметры выполнения параллельных запросов. Параллельные запросы между секциями позволяют выполнять запросы с низкой задержкой. Например, следующий запрос настроен на выполнение параллельно по секциям.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Вы можете управлять параллельным выполнением запросов, регулируя следующие параметры.

- Поле **MaxDegreeOfParallelism** устанавливает максимальное количество одновременных сетевых подключений к секциям контейнера. Если для параметра установить значение -1, пакет SDK будет регулировать степень параллелизма. Если параметр `MaxDegreeOfParallelism` не указан или имеет значение 0 (значение по умолчанию), к секциям контейнера установлено одно сетевое подключение.

- **MaxBufferedItemCount**: задержка запросов на транзакции и использование памяти на стороне клиента. Если пропустить этот параметр или задать значение -1, пакет SDK будет регулировать число буферизованных элементов при параллельном выполнении запросов.

При неизменном состоянии коллекции параллельные запросы будут возвращать результаты в том же порядке, что и при последовательном выполнении. При выполнении межсекционного запроса, включающего в себя операторы сортировки (ORDER BY, TOP), пакет SDK для Azure Cosmos DB параллельно выполняет запрос между секциями. Он объединяет частично отсортированные результаты на стороне клиента для получения глобально упорядоченных результатов.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать о секционировании в Azure Cosmos DB, ознакомьтесь со следующими статьями:

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Секционирование в Azure Cosmos DB)
- [Create a synthetic partition key](synthetic-partition-keys.md) (Создание искусственного ключа секций)
