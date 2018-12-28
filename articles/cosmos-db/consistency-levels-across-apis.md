---
title: Уровни согласованности и API для Azure Cosmos DB
description: Основные сведения об уровнях согласованности в API Azure Cosmos DB.
keywords: consistency, azure cosmos db, azure, models, mongodb, cassandra, graph, table, Microsoft azure
services: cosmos-db
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 277a064d93e2ebcea82f3909b3fd16328a775105
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832502"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Уровни согласованности и API для Azure Cosmos DB

SQL API Azure Cosmos DB изначально поддерживает пять моделей согласованности, предлагаемых Azure Cosmos DB. При использовании Azure Cosmos DB API-интерфейс SQL используется по умолчанию. 

Azure Cosmos DB также поддерживает API-интерфейсы, совместимые с проводными протоколами для популярных баз данных. Базы данных включают в себя MongoDB, Apache Cassandra, Gremlin и хранилище таблиц Azure. Эти базы данных не предоставляют ни точно определенных моделей согласованности, ни гарантий, обеспечиваемых Соглашением об уровне обслуживания, для уровней согласованности. Они обычно предоставляют лишь подмножество пяти моделей согласованности, предлагаемых Azure Cosmos DB. Для API SQL, API Gremlin и API таблиц используется уровень согласованности по умолчанию, настроенный для учетной записи Azure Cosmos DB. 

В следующих разделах показано соответствие между согласованностью данных, запрашиваемых драйвером клиента OSS для Apache Cassandra 4.x и MongoDB 3.4. Здесь также приведены соответствующие уровни согласованности Azure Cosmos DB для Apache Cassandra и MongoDB.

## <a id="cassandra-mapping"></a>Сопоставление уровней согласованности Apache Cassandra и Azure Cosmos DB

В этой таблице показано сопоставление согласованности чтения между клиентом Apache Cassandra 4.x и уровнем согласованности по умолчанию в Azure Cosmos DB. В таблице показаны развертывания в одном или нескольких регионах.

| **Apache Cassandra 4.x** | **Azure Cosmos DB (в нескольких регионах)** | **Azure Cosmos DB (в одном регионе)** |
| - | - | - |
| ONE, TWO, THREE | Согласованность префиксов | Согласованность префиксов |
| LOCAL_ONE | Согласованность префиксов | Согласованность префиксов |
| QUORUM, ALL, SERIAL | По умолчанию используется ограниченное устаревание. Уровень согласованности "строгая" находится в режиме закрытой предварительной версии. | Уровень согласованности Strong (сильная) |
| LOCAL_QUORUM | Ограниченная с запаздыванием | Уровень согласованности Strong (сильная) |
| LOCAL_SERIAL | Ограниченная с запаздыванием | Уровень согласованности Strong (сильная) |

## <a id="mongo-mapping"></a>Сопоставление уровней согласованности MongoDB 3.4 и Azure Cosmos DB

В следующей таблице показано сопоставление согласованности чтения между клиентом MongoDB 3.4 и уровнем согласованности по умолчанию в Azure Cosmos DB. В таблице показаны развертывания в одном или нескольких регионах.

| **MongoDB 3.4** | **Azure Cosmos DB (в нескольких регионах)** | **Azure Cosmos DB (в одном регионе)** |
| - | - | - |
| Линеаризуемость | Уровень согласованности Strong (сильная) | Уровень согласованности Strong (сильная) |
| Большинство | Ограниченная с запаздыванием | Уровень согласованности Strong (сильная) |
| Local | Согласованность префиксов | Согласованность префиксов |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об уровнях согласованности и совместимости API Azure Cosmos DB и API с открытым исходным кодом. Ознакомьтесь со следующими статьями:

* [Достижение компромисса между доступностью и быстродействием для разных уровней согласованности](consistency-levels-tradeoffs.md)
* [Поддержка возможностей и синтаксиса Azure Cosmos DB MongoDB в API-интерфейсе MongoDB](mongodb-feature-support.md)
* [Функции Apache Cassandra, поддерживаемые API Cassandra для Azure Cosmos DB](cassandra-support.md)