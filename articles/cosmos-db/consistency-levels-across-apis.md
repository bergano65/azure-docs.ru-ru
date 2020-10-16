---
title: Уровни согласованности и API для Azure Cosmos DB
description: Основные сведения о сопоставлении уровней согласованности между различными интерфейсами API в Azure Cosmos DB и Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/6/2020
ms.reviewer: sngun
ms.openlocfilehash: bb8a413f2e2a3aa4a8facd533d822312bb61fa0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613566"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Уровни согласованности и API для Azure Cosmos DB

Azure Cosmos DB предоставляет встроенную поддержку интерфейсов API, совместимых с протоколом Wired, для популярных баз данных. К ним относятся MongoDB, Apache Cassandra, Gremlin и хранилище таблиц Azure. Эти базы данных не предлагают точно определенные модели согласованности или гарантии с поддержкой соглашения об уровне обслуживания для уровней согласованности. Они обычно предоставляют лишь подмножество пяти моделей согласованности, предлагаемых Azure Cosmos DB.

При использовании API SQL, API Gremlin и API таблиц используется уровень согласованности по умолчанию, настроенный в учетной записи Azure Cosmos. 

При использовании API Cassandra или API Azure Cosmos DB для MongoDB приложения получают полный набор уровней согласованности, предлагаемых Apache Cassandra и MongoDB соответственно, с более надежными гарантиями согласованности и устойчивости. В этом документе показаны соответствующие уровни согласованности Azure Cosmos DB для уровней согласованности Apache Cassandra и MongoDB.

> [!NOTE]
> По умолчанию для Azure Cosmos DB используется модель согласованности Session. Session — это клиентская модель согласованности, которая изначально не поддерживается ни в Cassandra, ни в MongoDB. Дополнительные сведения о выборе модели согласованности см. в разделе [уровни согласованности в Azure Cosmos DB](consistency-levels.md)

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Сопоставление уровней согласованности Apache Cassandra и Azure Cosmos DB

В отличие от Azure Cosmos DB, Apache Cassandra изначально не предоставляет точно определенные гарантии согласованности.  Вместо этого Apache Cassandra предоставляет уровень согласованности записи и уровень согласованности чтения, чтобы обеспечить высокую доступность, согласованность и задержку. При использовании API Cassandra Azure Cosmos DB: 

* Уровень согласованности записи Apache Cassandra сопоставлен с уровнем согласованности по умолчанию, настроенным в вашей учетной записи Azure Cosmos. Согласованность для операции записи (CL) не может быть изменена отдельно для каждого запроса.

* Azure Cosmos DB будет динамически сопоставлять уровень согласованности чтения, заданный драйвером клиента Cassandra, с одним из Azure Cosmos DBных уровней согласованности, динамически настроенных для запроса на чтение. 

В следующей таблице показано, как собственные уровни согласованности Cassandra сопоставляются с уровнями согласованности Azure Cosmos DB при использовании API Cassandra.  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Сопоставление модели согласованности Cassandra" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Сопоставление уровней согласованности MongoDB и Azure Cosmos DB

В отличие от Azure Cosmos DB, машинный MongoDB не предоставляет точно определенные гарантии согласованности. Вместо этого Native MongoDB позволяет пользователям настраивать следующие гарантии согласованности: запись, проблема чтения и директива «Master» — для направления операций чтения к первичным или вторичным репликам для достижения требуемого уровня согласованности.

При использовании API Azure Cosmos DB для MongoDB драйвер MongoDB считает регион записи первичной репликой, а все остальные регионы — репликой чтения. Вы можете выбрать регион, связанный с вашей учетной записью Azure Cosmos в качестве первичной реплики. 

При использовании API Azure Cosmos DB для MongoDB:

* Проблема записи сопоставлена с уровнем согласованности по умолчанию, настроенным в учетной записи Azure Cosmos.

* Azure Cosmos DB динамически сопоставляет проблему чтения, указанную драйвером клиента MongoDB, с одним из Azure Cosmos DBных уровней согласованности, динамически настроенных для запроса на чтение.  

* Вы можете добавить заметки к определенному региону, связанному с вашей учетной записью Azure Cosmos, как "основной", сделав этот регион первым доступным для записи регионом. 

В следующей таблице показано, как проблемы с записью и чтением в машинном MongoDB сопоставляются с уровнями согласованности Azure Cosmos при использовании API Azure Cosmos DB для MongoDB:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Сопоставление модели согласованности Cassandra" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения об уровнях согласованности и совместимости API Azure Cosmos DB и API с открытым исходным кодом. Дополнительные сведения см. в следующих статьях:

* [Достижение компромисса между доступностью и быстродействием для разных уровней согласованности](consistency-levels-tradeoffs.md)
* [Использование API Azure Cosmos DB для поддержки возможностей и синтаксиса MongoDB](mongodb-feature-support.md)
* [Функции Apache Cassandra, поддерживаемые API Cassandra для Azure Cosmos DB](cassandra-support.md)
