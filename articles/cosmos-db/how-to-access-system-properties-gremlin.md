---
title: Доступ к системным свойствам документа через Graph для Azure Cosmos DB
description: Сведения о том, как считывать и записывать свойства документа Cosmos DB с помощью API Gremlin
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: e762674936ab2fbdf198ca67f79acfa545127f02
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755073"
---
# <a name="system-document-properties"></a>Системные свойства документа

Каждый документ в Azure Cosmos DB содержит [системные свойства](https://docs.microsoft.com/rest/api/cosmos-db/databases) например ```_ts```, ```_self```, ```_attachments```, ```_rid``` и ```_etag```. Кроме того, обработчик Gremlin добавляет в конце и в начале свойства ```inVPartition``` и ```outVPartition```. По умолчанию эти свойства доступны для обхода. Но определенные (или все) свойства можно включить в операцию обхода Gremlin.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>ETag

Это свойство используется для управления оптимистической блокировкой. Если приложению требуется разбить операцию на несколько отдельных проходов, оно может использовать свойство ETag, чтобы избежать потери данных при параллельных операциях записи.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Срок жизни (TTL)

Если в коллекции включено окончание срока действия документов, а для документов задано свойство ```ttl```, это свойство будет доступно в операции обхода Gremlin как обычное свойство вершины или ребра. ```ProjectionStrategy``` не обязательно использовать для обеспечения доступности свойства TTL.

Вершина, созданная с помощью указанного ниже обхода, будет автоматически удалена через **123 секунды**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Дополнительная информация
* [Оптимистическая блокировка в Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Срок жизни](time-to-live.md) в Azure Cosmos DB
