---
title: Уровни согласованности и API для Azure Cosmos DB
description: Понимание отображения уровня согласованности между различными AБИ в Azure Cosmos DB и Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: ef7d032d37105549ff7b05f85b953cd420954602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131456"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Уровни согласованности и API для Azure Cosmos DB

Azure Cosmos DB обеспечивает нативную поддержку aI-аПО, совместимых с протоколами проводов, для популярных баз данных. К ним относятся хранение MongoDB, Apache Cassandra, Gremlin и Azure Table. Эти базы данных не предлагают точно определенных моделей согласованности или гарантий sLA для уровней согласованности. Они обычно предоставляют лишь подмножество пяти моделей согласованности, предлагаемых Azure Cosmos DB. 

При использовании API, API Gremlin и Table API используется уровень согласованности по умолчанию, настроенный на учетную запись Azure Cosmos. 

При использовании API Cassandra API или API Azure Cosmos DB для MongoDB приложения получают полный набор уровней согласованности, предлагаемых Apache Cassandra и MongoDB, соответственно, с еще более сильными гарантиями согласованности и долговечности. В этом документе показаны соответствующие уровни согласованности Azure Cosmos DB для уровней согласованности Apache Cassandra и MongoDB.

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Сопоставление уровней согласованности Apache Cassandra и Azure Cosmos DB

В отличие от Azure Cosmos DB, Apache Cassandra не обеспечивает точно определенных гарантий согласованности.  Вместо этого Apache Cassandra обеспечивает уровень согласованности записей и уровень согласованности чтения, чтобы обеспечить высокую доступность, согласованность и задержку. При использовании API Cassandra API Azure Cosmos DB: 

* Уровень согласованности записи Apache Cassandra отображен на уровне согласованности по умолчанию, настроенном на учетную запись Azure Cosmos. Согласованность операции записи (CL) не может быть изменена на основе запроса.

* Azure Cosmos DB динамически сопоставит уровень согласованности чтения, указанный драйвером клиента Cassandra, к одному из уровней согласованности Azure Cosmos DB, настроенного динамически по запросу чтения. 

В следующей таблице показано, как уровни согласованности «Кассандра» отображаются на уровнях согласованности Azure Cosmos DB при использовании API Cassandra API:  

[![Картирование модели консистенции Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Сопоставление между уровнями согласованности MongoDB и Azure Cosmos DB

В отличие от Azure Cosmos DB, родной MongoDB не дает точно определенных гарантий согласованности. Вместо этого, родной MongoDB позволяет пользователям настроить следующие гарантии согласованности: написать озабоченность, читать озабоченность, и директива isMaster - направить чтение операций либо первичных или вторичных реплик для достижения желаемого уровня согласованности. 

При использовании API-извращенца Azure Cosmos DB для MongoDB драйвер MongoDB рассматривает область записи как основную реплику, а все остальные регионы читаются репликами. Вы можете выбрать, какой регион связан с учетной записью Azure Cosmos в качестве основной реплики. 

При использовании API-изира для MongoDB Azure Cosmos DB:

* Концерн записей отображается на уровне согласованности по умолчанию, настроенном на учетную запись Azure Cosmos.
 
* Azure Cosmos DB динамически сопоставит с читающей проблемой, указанной драйвером клиента MongoDB, к одному из уровней согласованности Azure Cosmos DB, настроенного динамически по запросу чтения.  

* Можно аннотировать определенный регион, связанный с вашей учетной записью Azure Cosmos как "Мастер", сделав регион первым областью, которая может быть вычтена. 

В следующей таблице показано, как родные проблемы MongoDB отображаются на уровнях согласованности Azure Cosmos при использовании API Azure Cosmos DB для MongoDB:

[![Отображение модели согласованности MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об уровнях согласованности и совместимости API Azure Cosmos DB и API с открытым исходным кодом. См. следующие статьи:

* [Достижение компромисса между доступностью и быстродействием для разных уровней согласованности](consistency-levels-tradeoffs.md)
* [Использование API Azure Cosmos DB для поддержки возможностей и синтаксиса MongoDB](mongodb-feature-support.md)
* [Функции Apache Cassandra, поддерживаемые API Cassandra для Azure Cosmos DB](cassandra-support.md)
