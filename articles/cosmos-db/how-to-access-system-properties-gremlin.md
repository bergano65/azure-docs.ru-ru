---
title: Доступ к системным свойствам документа через Graph для Azure Cosmos DB
description: Сведения о том, как считывать и записывать свойства документа Cosmos DB с помощью API Gremlin
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: a0feac9bbd98dc4c67464e84e9a3204bd9730355
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390352"
---
# <a name="system-document-properties"></a>Системные свойства документа

Azure Cosmos DB имеет [системные свойства](/rest/api/cosmos-db/databases) , такие как ```_ts``` ,, ```_self``` ```_attachments``` , ```_rid``` и ```_etag``` в каждом документе. Кроме того, обработчик Gremlin добавляет в конце и в начале свойства ```inVPartition``` и ```outVPartition```. По умолчанию эти свойства доступны для обхода. Но определенные (или все) свойства можно включить в операцию обхода Gremlin.

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

## <a name="next-steps"></a>Дальнейшие шаги
* [Оптимистическая блокировка в Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* Срок [жизни (TTL)](time-to-live.md) в Azure Cosmos DB
