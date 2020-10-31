---
title: Уровни согласованности Apache Cassandra и Azure Cosmos DB
description: Уровни согласованности Apache Cassandra и Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: f640ad85fd34dd5a4803e7dd96f1c0283f0c859a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100437"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Уровни согласованности Apache Cassandra и Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

В отличие от Azure Cosmos DB, Apache Cassandra изначально не предоставляет точно определенные гарантии согласованности. Вместо этого Apache Cassandra предоставляет уровень согласованности записи и уровень согласованности чтения, чтобы обеспечить высокую доступность, согласованность и задержку. При использовании API Cassandra Azure Cosmos DB:

* Уровень согласованности записи Apache Cassandra сопоставлен с уровнем согласованности по умолчанию, настроенным в вашей учетной записи Azure Cosmos. Согласованность для операции записи (CL) не может быть изменена отдельно для каждого запроса.

* Azure Cosmos DB будет динамически сопоставлять уровень согласованности чтения, заданный драйвером клиента Cassandra, с одним из Azure Cosmos DBных уровней согласованности, динамически настроенных для запроса на чтение.

## <a name="mapping-consistency-levels"></a>Сопоставление уровней согласованности

В следующей таблице показано, как собственные уровни согласованности Cassandra сопоставляются с уровнями согласованности Azure Cosmos DB при использовании API Cassandra.  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Сопоставление модели согласованности Cassandra" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Если ваша учетная запись Azure Cosmos настроена с уровнем согласованности, отличным от строгой согласованности, можно выяснить вероятность того, что клиенты могут получить надежные и согласованные операции чтения для рабочих нагрузок, просмотрев метрику *Probabilistically ограниченного устаревания* (PBS). Эта метрика предоставляется на портале Azure. Дополнительные сведения см. в разделе [Мониторинг метрики вероятностного ограниченного устаревания (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Вероятностное ограниченное устаревание показывает степень итоговой согласованности. Эта метрика позволяет понять, насколько часто можно добиться более высокой согласованности, чем уровень согласованности, настроенный в вашей учетной записи Azure Cosmos. Другими словами, вы можете увидеть вероятность (в миллисекундах) выполнения строго согласованных операций чтения для комбинации регионов записи и чтения.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о глобальных уровнях распределения и согласованности для Azure Cosmos DB:

* [Общие сведения о глобальном распределении](distribute-data-globally.md)
* [Обзор уровня согласованности](consistency-levels.md)
* [Высокая доступность](high-availability.md)
