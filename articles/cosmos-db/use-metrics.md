---
title: Мониторинг и отладка с помощью метрик в Azure Cosmos DB
description: Сведения об отладке распространенных проблем и мониторинге базы данных с помощью метрик в Azure Cosmos DB.
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 11/15/2018
ms.reviewer: sngun
ms.openlocfilehash: ff6e0b6084eebf236d01b4dd00a46897687938c2
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034732"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Мониторинг и отладка с помощью метрик в Azure Cosmos DB

Azure Cosmos DB предоставляет метрики пропускной способности, хранилища, согласованности, доступности и задержки. На [портале Azure](https://portal.azure.com) отображается общее представление этих метрик. Более детальные метрики доступны в клиентском пакете SDK и [журналах диагностики](./logging.md).

В этой статье рассматриваются распространенные варианты использования Azure Cosmos DB, а также представлены сведения об анализе и отладке проблем в этой базе данных на основе метрик. Сбор метрик осуществляется каждые пять минут. Они хранятся в течение семи дней.

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Сведения о количестве успешных запросов и запросов, приводящих к ошибкам

Чтобы приступить к работе, перейдите на [портал Azure](https://portal.azure.com) и откройте колонку **Метрики**. В этой колонке найдите диаграмму **Число запросов, которые превысили емкость, за минуту**. На этой диаграмме показано общее количество запросов за минуту, разбитых по коду состояния. Дополнительные сведения о кодах состояния HTTP для Azure Cosmos DB см. в [этой статье](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Наиболее распространенный код состояния ошибки — 429 (ограничение скорости и регулирование). Эта ошибка означает, что запросы к Azure Cosmos DB превышают квоту подготовленной пропускной способности. Чтобы решить эту проблему, чаще всего следует [увеличить число единиц запроса](./set-throughput.md) определенной коллекции.

![Число запросов в минуту](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Определение распределения пропускной способности по секциям

При использовании масштабируемого приложения важно иметь достаточное количество ключей секции. Чтобы определить распределение пропускной способности для любого секционированного контейнера, перейдите в колонку **Метрики** на [портале Azure](https://portal.azure.com). На вкладке **Пропускная способность** на диаграмме **Max consumed RU/second by each physical partition** (Максимальное число потребленных единиц запроса каждой физической секцией за секунду) отображаются показатели хранилища. На графике ниже приведен пример неравномерного распределения данных, о чем свидетельствует секция слева.

![Высокая интенсивность использования секции в 15:05](media/use-metrics/metrics-17.png)

Неравномерное распределение пропускной способности может привести к образованию секций с *высокой нагрузкой*, из-за чего могут возникать отрегулированные запросы и может потребоваться повторное секционирование. Дополнительные сведения о секционировании в Azure Cosmos DB см. в [этой статье](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Определение распределения ресурсов хранения по секциям

При использовании масштабируемого приложения важно иметь достаточное количество секций. Чтобы определить распределение ресурсов хранения для любого секционированного контейнера, перейдите в колонку "Метрики" на [портале Azure](https://portal.azure.com). На вкладке "Хранилище" сведения о ресурсах хранения отображаются в диаграмме "Объем хранилища данных и индекса, занятый ведущими ключами секций". На графике ниже приведено неравномерное распределение ресурсов хранения данных, о чем свидетельствует секция слева.

![Пример неравномерного распределения данных](media/use-metrics/metrics-07.png)

Чтобы определить, какой ключ секции искажает распределение, щелкните секцию на диаграмме.

![Ключ секции, искажающий распределение](media/use-metrics/metrics-05.png)

После определения ключа секции, искажающего распределение, вы можете повторно разбить контейнер с большим числом распределенных ключей секции. Дополнительные сведения о секционировании в Azure Cosmos DB см. в [этой статье](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Сравнение размера данных и размера индекса

В базе данных Azure Cosmos DB общий использованный объем хранилища зависит от размера данных и размера индекса. Обычно размер индекса — это часть объема данных. В колонке "Метрики" [портала Azure](https://portal.azure.com) на вкладке "Хранилище" можно просмотреть сведения о потреблении хранилища на основе данных и индекса.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Чтобы сэкономить пространство индекса, вы можете настроить [политику индексирования](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Устранение причины медленного выполнения запросов

В пакетах SDK для API SQL можно просмотреть статистику выполнения запросов в базе данных Azure Cosmos DB.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

Свойство *QueryMetrics* предоставляет подробные сведения о длительности выполнения каждого компонента запроса. Основная первопричина медленного выполнения запросов — сканирования. Это означает, что запрос не может использовать индексы. Эту проблему можно устранить, настроив оптимальное условие фильтра.

## <a name="next-steps"></a>Дополнительная информация

Теперь вы знаете, как отслеживать и отлаживать неполадки с помощью метрик, предоставленных на портале Azure. Узнать больше о повышении производительности базы данных можно из следующих статей:

* [Проверка производительности и масштабирования с помощью Azure Cosmos DB](performance-testing.md)
* [Советы по повышению производительности для Azure Cosmos DB](performance-tips.md)
