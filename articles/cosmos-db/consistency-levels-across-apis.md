---
title: Уровни согласованности и API для Azure Cosmos DB
description: Основные сведения об уровнях согласованности в API Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 6779e434339935b64440d7cf617c8946338e36e3
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756983"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Уровни согласованности и API для Azure Cosmos DB

Azure Cosmos DB предоставляет встроенную поддержку интерфейсов API, совместимых с протоколом Wired, для популярных баз данных. К ним относятся MongoDB, Apache Cassandra, Gremlin и хранилище таблиц Azure. Эти базы данных не предлагают точно определенные модели согласованности или гарантии с поддержкой соглашения об уровне обслуживания для уровней согласованности. Они обычно предоставляют лишь подмножество пяти моделей согласованности, предлагаемых Azure Cosmos DB. 

При использовании API SQL, API Gremlin и API таблиц используется уровень согласованности по умолчанию, настроенный в учетной записи Azure Cosmos. 

При использовании API Cassandra или API Azure Cosmos DB для MongoDB приложения получают полный набор уровней согласованности, предлагаемых Apache Cassandra и MongoDB соответственно, с более надежными гарантиями согласованности и устойчивости. В этом документе показаны соответствующие уровни согласованности Azure Cosmos DB для уровней согласованности Apache Cassandra и MongoDB.


## <a id="cassandra-mapping"></a>Сопоставление уровней согласованности Apache Cassandra и Azure Cosmos DB

В отличие от Азурекосмос DB, Apache Cassandra изначально не предоставляет точно определенные гарантии согласованности.  Вместо этого Apache Cassandra предоставляет уровень согласованности записи и уровень согласованности чтения, чтобы обеспечить высокую доступность, согласованность и задержку. При использовании API Cassandra Azure Cosmos DB: 

* Уровень согласованности записи Apache Cassandra сопоставлен с уровнем согласованности по умолчанию, настроенным в вашей учетной записи Azure Cosmos. 

* Azure Cosmos DB будет динамически сопоставлять уровень согласованности чтения, заданный драйвером клиента Cassandra, с одним из Azure Cosmos DBных уровней согласованности, динамически настроенных для запроса на чтение. 

В следующей таблице показано, как собственные уровни согласованности Cassandra сопоставляются с уровнями согласованности Azure Cosmos DB при использовании API Cassandra.  

[сопоставление модели согласованности ![Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Сопоставление уровней согласованности MongoDB и Azure Cosmos DB

В отличие от Azure Cosmos DB, машинный MongoDB не предоставляет точно определенные гарантии согласованности. Вместо этого Native MongoDB позволяет пользователям настраивать следующие гарантии согласованности: запись, проблема чтения и директива «Master» — для направления операций чтения к первичным или вторичным репликам для достижения требуемого уровня согласованности. 

При использовании API Azure Cosmos DB для MongoDB драйвер MongoDB считает регион записи первичной репликой, а все остальные регионы — репликой чтения. Вы можете выбрать регион, связанный с вашей учетной записью Azure Cosmos в качестве первичной реплики. 

При использовании API Azure Cosmos DB для MongoDB:

* Проблема записи сопоставлена с уровнем согласованности по умолчанию, настроенным в учетной записи Azure Cosmos.
 
* Azure Cosmos DB динамически сопоставляет проблему чтения, указанную драйвером клиента MongoDB, с одним из Azure Cosmos DBных уровней согласованности, динамически настроенных для запроса на чтение. 

* Вы можете добавить заметки к определенному региону, связанному с вашей учетной записью Azure Cosmos, в качестве "главного", сделав этот регион первым доступным для записи регионом. 

В следующей таблице показано, как проблемы с записью и чтением в машинном MongoDB сопоставляются с уровнями согласованности Azure Cosmos при использовании API Azure Cosmos DB для MongoDB:

[сопоставление модели согласованности ![MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об уровнях согласованности и совместимости API Azure Cosmos DB и API с открытым исходным кодом. Ознакомьтесь со следующими статьями:

* [Достижение компромисса между доступностью и быстродействием для разных уровней согласованности](consistency-levels-tradeoffs.md)
* [Использование API Azure Cosmos DB для поддержки возможностей и синтаксиса MongoDB](mongodb-feature-support.md)
* [Функции Apache Cassandra, поддерживаемые API Cassandra для Azure Cosmos DB](cassandra-support.md)