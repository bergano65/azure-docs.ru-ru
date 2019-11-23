---
title: Пропускная способность и единицы запросов в Azure Cosmos DB
description: Сведения о том, как задать и оценить количество необходимых единиц запросов в Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383110"
---
# <a name="request-units-in-azure-cosmos-db"></a>Единицы запросов в базе данных Azure Cosmos DB

При использовании Azure Cosmos DB вы платите на почасовой основе за пропускную способность, которую предоставляете, и за ресурсы хранения, которое вы потребляете. Throughput must be provisioned to ensure that sufficient system resources are available for your Azure Cosmos database at all times. You need enough resources to meet or exceed the [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB поддерживает многие интерфейсы API, такие как SQL, MongoDB, Cassandra, Gremlin и Таблицы. У каждого API есть собственный набор операций базы данных, начиная от простого считывания и записи точек и заканчивая сложными запросами. Каждая операция базы данных потребляет системные ресурсы. Потребление зависит от сложности операции. 

The cost of all database operations is normalized by Azure Cosmos DB and is expressed by *Request Units* (or RUs, for short). Вы можете считать ЕЗ/с в качестве пропускной способности. ЕЗ/с — это единица, основанная на тарифе. Он абстрагирует системные ресурсы (например, ЦП, операции ввода-вывода в секунду и память), необходимые для выполнения операций базы данных, поддерживаемых Azure Cosmos DB. 

The cost to read a 1 KB item is 1 Request Unit (or 1 RU). A minimum of 10 RU/s is required to store each 1 GB of data. All other database operations are similarly assigned a cost using RUs. Независимо от того, какие API вы используете для взаимодействия с контейнером Azure Cosmos, затраты всегда измеряются в ЕЗ. Независимо от типа операции базы данных (запись, чтение или запрос) затраты всегда измеряются в ЕЗ.

The following image shows the high-level idea of RUs:

![Операции с базой данных потребляют единицы запроса](./media/request-units/request-units.png)

Чтобы обеспечить планирование пропускной способности и управление ею, Azure Cosmos DB гарантирует, что количество ЕЗ для операции базы данных над определенным набором данных является детерминированным. You can examine the response header to track the number of RUs that are consumed by any database operation. When you understand the [factors that affect RU charges](request-units.md#request-unit-considerations) and your application's throughput requirements, you can run your application cost effectively.

Количество ЕЗ для приложения подготавливается на основе данных, получаемых в секунду, с шагом в 100 ЕЗ/с. Чтобы масштабировать подготовленную пропускную способность для приложения, вы можете увеличить или уменьшить количество единиц запросов в любое время. You can scale in increments or decrements of 100 RUs. Это можно сделать программным способом или с помощью портала Azure. You are billed on an hourly basis.

Вы можете обеспечить пропускную способность с помощью двух различных уровней детализаций: 

* **Containers**: For more information, see [Provision throughput on an Azure Cosmos container](how-to-provision-container-throughput.md).
* **Databases**: For more information, see [Provision throughput on an Azure Cosmos database](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Рекомендации по оценке необходимого количества единиц запросов

Когда вы оцениваете количество ЕЗ в секунду для подготовки, учитывайте следующие факторы:

* **Item size**: As the size of an item increases, the number of RUs consumed to read or write the item also increases.

* **Item indexing**: By default, each item is automatically indexed. Если не индексировать некоторые элементы в контейнере, будет использовано меньше единиц запросов.

* **Item property count**: Assuming the default indexing is on all properties, the number of RUs consumed to write an item increases as the item property count increases.

* **Indexed properties**: An index policy on each container determines which properties are indexed by default. Чтобы сократить потребление единиц запросов для операций записи, ограничьте количество индексируемых свойств.

* **Data consistency**: The strong and bounded staleness consistency levels consume approximately two times more RUs while performing read operations when compared to that of other relaxed consistency levels.

* **Query patterns**: The complexity of a query affects how many RUs are consumed for an operation. Факторы, которые влияют на стоимость операций запросов: 
    
    - The number of query results
    - The number of predicates
    - The nature of the predicates
    - The number of user-defined functions
    - The size of the source data
    - The size of the result set
    - Проекции

  Azure Cosmos DB гарантирует, что при повторном выполнении один и тот же запрос к тем же данным всегда будет стоить то же количество ЕЗ.

* **Script usage**: As with queries, stored procedures and triggers consume RUs based on the complexity of the operations that are performed. As you develop your application, inspect the [request charge header](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) to better understand how much RU capacity each operation consumes.

## <a name="next-steps"></a>Дальнейшие действия

* Подробнее о [подготовке пропускной способности в контейнерах и базах данных Azure Cosmos](set-throughput.md).
* Подробнее о [логических разделах](partition-data.md).
* Подробнее о [глобальном масштабировании подготовленной пропускной способности](scaling-throughput.md).
* Подробнее о [подготовке пропускной способности для контейнера Azure Cosmos](how-to-provision-container-throughput.md).
* Подробнее о [подготовке пропускной способности для базы данных Azure Cosmos](how-to-provision-database-throughput.md).
* Learn how to [find the request unit charge for an operation](find-request-unit-charge.md).
* Learn how to [optimize provisioned throughput cost in Azure Cosmos DB](optimize-cost-throughput.md).
* Learn how to [optimize reads and writes cost in Azure Cosmos DB](optimize-cost-reads-writes.md).
* Learn how to [optimize query cost in Azure Cosmos DB](optimize-cost-queries.md).
* Learn how to [use metrics to monitor throughput](use-metrics.md).
