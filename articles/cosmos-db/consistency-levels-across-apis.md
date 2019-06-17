---
title: Уровни согласованности и API для Azure Cosmos DB
description: Основные сведения об уровнях согласованности в API Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: sngun
ms.openlocfilehash: 1129152c1823fbffb3d6c9ec918d7b8cb4426bbd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235626"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Уровни согласованности и API для Azure Cosmos DB

Azure Cosmos DB обеспечивает встроенную поддержку передачи совместимую по протоколу API-интерфейсы для популярными базами данных. К ним относятся MongoDB, Apache Cassandra, Gremlin и хранилище таблиц Azure. Эти базы данных не предлагают точно определенных моделей согласованности или от соглашения об уровне ОБСЛУЖИВАНИЯ с гарантией гарантии для этих уровней согласованности. Они обычно предоставляют лишь подмножество пяти моделей согласованности, предлагаемых Azure Cosmos DB. 

При использовании API-интерфейса SQL, Gremlin API и API таблиц, используется уровень согласованности по умолчанию, настроенные в учетной записи Azure Cosmos. 

При использовании Cassandra API или Azure Cosmos DB API для MongoDB, приложения получают полный набор уровней согласованности, предлагаемых Apache Cassandra и MongoDB, соответственно, даже строгим уровнем согласованности и гарантии надежности. В этом документе показано соответствующих уровней согласованности Azure Cosmos DB для Apache Cassandra и MongoDB уровней согласованности.


## <a id="cassandra-mapping"></a>Сопоставление уровней согласованности Apache Cassandra и Azure Cosmos DB

В отличие от AzureCosmos DB Apache Cassandra не содержит точного гарантии согласованности.  Вместо этого Apache Cassandra предоставляет уровень согласованности записи и уровень согласованности для чтения, чтобы включить высокий уровень доступности, согласованности и задержки компромиссы. При использовании API Cassandra Azure Cosmos DB: 

* Уровень согласованности записи Apache Cassandra сопоставляется уровня согласованности по умолчанию, настроенного в учетной записи Azure Cosmos. 

* Azure Cosmos DB динамически сопоставит уровень согласованности для чтения, определяемое драйвер клиента Cassandra на один из этих уровней согласованности Azure Cosmos DB, динамически настроен на запрос на чтение. 

В следующей таблице показано, как собственные уровни согласованности Cassandra сопоставляются с Azure Cosmos DB уровней согласованности при использовании API-интерфейса Cassandra:  

[![Сопоставления модели согласованности Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Сопоставление MongoDB и Azure Cosmos DB уровней согласованности

В отличие от Azure Cosmos DB собственный MongoDB не поддерживает точно гарантии согласованности. Вместо этого собственного MongoDB позволяет пользователям настраивать следующие гарантии согласованности: значения записи, чтения проблемой и директивы isMaster - направлять операции чтения в первичный или вторичный реплик для достижения требуемой согласованности уровня. 

При использовании API Azure Cosmos DB для MongoDB, драйвер MongoDB считает своем регионе записи первичной реплики и другие регионы доступны для чтения реплики. Вы можете выбрать, какой регион, связанный с вашей учетной записи Azure Cosmos в качестве первичной реплики. 

При использовании API Azure Cosmos DB для MongoDB:

* Тем, что записи сопоставляется уровня согласованности по умолчанию, настроенного в учетной записи Azure Cosmos.
 
* Azure Cosmos DB динамически сопоставит чтения тем, что указанный драйвер клиента MongoDB к одному из этих уровней согласованности Azure Cosmos DB, которые динамически настраивается на запрос на чтение. 

* Можно добавить заметку в определенном регионе, связанный с вашей учетной записи Azure Cosmos, как «Master», сделав регионе, что первый доступный для записи регион. 

В следующей таблице показано, как собственный MongoDB чтения и записи проблемы сопоставляются уровней согласованности Azure Cosmos, при использовании API Azure Cosmos DB для MongoDB:

[![Сопоставления модели согласованности MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об уровнях согласованности и совместимости API Azure Cosmos DB и API с открытым исходным кодом. Ознакомьтесь со следующими статьями:

* [Достижение компромисса между доступностью и быстродействием для разных уровней согласованности](consistency-levels-tradeoffs.md)
* [Использование API Azure Cosmos DB для поддержки возможностей и синтаксиса MongoDB](mongodb-feature-support.md)
* [Функции Apache Cassandra, поддерживаемые API Cassandra для Azure Cosmos DB](cassandra-support.md)