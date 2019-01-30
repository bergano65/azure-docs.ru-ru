---
title: Уровни согласованности и API для Azure Cosmos DB
description: Основные сведения об уровнях согласованности в API Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 4d2994ea6ab6d6472ec56f0f2e378062590c8920
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807003"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Уровни согласованности и API для Azure Cosmos DB

SQL API Azure Cosmos DB изначально поддерживает пять моделей согласованности, предлагаемых Azure Cosmos DB. При использовании Azure Cosmos DB API-интерфейс SQL используется по умолчанию. 

Azure Cosmos DB также поддерживает API-интерфейсы, совместимые с проводными протоколами для популярных баз данных. Базы данных включают в себя MongoDB, Apache Cassandra, Gremlin и хранилище таблиц Azure. Эти базы данных не предоставляют ни точно определенных моделей согласованности, ни гарантий, обеспечиваемых Соглашением об уровне обслуживания, для уровней согласованности. Они обычно предоставляют лишь подмножество пяти моделей согласованности, предлагаемых Azure Cosmos DB. Для API SQL, API Gremlin и API таблиц используется уровень согласованности по умолчанию, настроенный для учетной записи Azure Cosmos DB. 

В следующих разделах показано соответствие между согласованностью данных, запрашиваемых драйвером клиента OSS для Apache Cassandra 4.x и MongoDB 3.4. Здесь также приведены соответствующие уровни согласованности Azure Cosmos DB для Apache Cassandra и MongoDB.

## <a id="cassandra-mapping"></a>Сопоставление уровней согласованности Apache Cassandra и Azure Cosmos DB

В этой таблице показано сопоставление согласованности между уровнями Apache Cassandra и согласованности в Azure Cosmos DB. Для каждого из уровней согласованности чтения и записи Cassandra соответствующий уровень согласованности Cosmos DB обеспечивает более надежные гарантии.

В следующей таблице показано **сопоставление согласованности записи** между Azure Cosmos DB и Cassandra.

| Cassandra | Azure Cosmos DB | Гарантия |
| - | - | - |
|ALL|Уровень согласованности Strong (сильная)  | Линеаризация |
| EACH_QUORUM   | Уровень согласованности Strong (сильная)    | Линеаризация | 
| QUORUM, SERIALQUORUM, SERIAL |  Уровень согласованности Strong (сильная) |    Линеаризация |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Согласованность префиксов |Глобальный согласованный префикс |
| EACH_QUORUM   | Уровень согласованности Strong (сильная)    | Линеаризация |
| QUORUM, SERIALQUORUM, SERIAL |  Уровень согласованности Strong (сильная) |    Линеаризация |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Согласованность префиксов | Глобальный согласованный префикс |
| QUORUM, SERIALQUORUM, SERIAL | Уровень согласованности Strong (сильная)   | Линеаризация |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Согласованность префиксов | Глобальный согласованный префикс |
| LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE    | Ограниченное устаревание | <ul><li>Ограниченное устаревание.</li><li>В большинстве K версий или прошедшего времени t.</li><li>Чтение последнего зафиксированного значения в регионе.</li></ul> |
| ONE, LOCAL_ONE, ANY   | Согласованность префиксов | Постоянный префикс для каждого региона |

В следующей таблице показано **сопоставление согласованности чтения** между Azure Cosmos DB и Cassandra.

| Cassandra | Azure Cosmos DB | Гарантия |
| - | - | - |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO, ONE, LOCAL_ONE | Уровень согласованности Strong (сильная)  | Линеаризация|
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Уровень согласованности Strong (сильная) |   Линеаризация |
|LOCAL_ONE, ONE | Согласованность префиксов | Глобальный согласованный префикс |
| ALL, QUORUM, SERIAL   | Уровень согласованности Strong (сильная)    | Линеаризация |
| LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  Согласованность префиксов   | Глобальный согласованный префикс |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM |    Согласованность префиксов   | Глобальный согласованный префикс |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Уровень согласованности Strong (сильная) |   Линеаризация |
| LOCAL_ONE, ONE    | Согласованность префиксов | Глобальный согласованный префикс|
| ALL, QUORUM, SERIAL строгая линеаризация
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |Согласованность префиксов  | Глобальный согласованный префикс |
|ALL    |Уровень согласованности Strong (сильная) |Линеаризация |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  |Согласованность префиксов  |Глобальный согласованный префикс|
|ALL, QUORUM, SERIAL строгая линеаризация
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |Согласованность префиксов  |Глобальный согласованный префикс |
|ALL    |Уровень согласованности Strong (сильная) | Линеаризация |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | Согласованность префиксов | Глобальный согласованный префикс |
| QUORUM, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  Ограниченное устаревание   | <ul><li>Ограниченное устаревание.</li><li>В большинстве K версий или прошедшего времени t. </li><li>Чтение последнего зафиксированного значения в регионе.</li></ul>
| LOCAL_ONE, ONE |Согласованность префиксов | Постоянный префикс для каждого региона |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | Согласованность префиксов | Постоянный префикс для каждого региона |


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
* [Использование API Azure Cosmos DB для поддержки возможностей и синтаксиса MongoDB](mongodb-feature-support.md)
* [Функции Apache Cassandra, поддерживаемые API Cassandra для Azure Cosmos DB](cassandra-support.md)