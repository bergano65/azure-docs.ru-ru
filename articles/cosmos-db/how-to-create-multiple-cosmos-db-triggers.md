---
title: Создание нескольких независимых триггеров Azure Cosmos DB
description: Из этой статьи вы узнаете, как настроить несколько независимых триггеров Azure Cosmos DB для создания архитектур на основе событий с помощью решения "Функции Azure".
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 4/15/2019
ms.author: maquaran
ms.openlocfilehash: 7a47a928e97d52535a6d3baa808f1fcb81d9bb55
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59700299"
---
# <a name="create-multiple-azure-cosmos-db-triggers"></a>Создание нескольких триггеров Azure Cosmos DB

В этой статье описывается, как настроить несколько триггеров Cosmos DB для работы в параллельном режиме и независимого реагирования на изменения.

![Бессерверные функции на основе событий, которые работают с триггером Azure Cosmos DB и совместно используют контейнер аренды](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Требования к архитектуре на основе событий

Если вы создаете бессерверные архитектуры с помощью решения [Функции Azure](../azure-functions/functions-overview.md), мы [рекомендуем](../azure-functions/functions-best-practices.md#avoid-long-running-functions) создавать небольшие наборы функций, которые работают совместно (а не крупные длительные функции).

Создавая бессерверные потоки на основе событий с помощью [триггера Azure Cosmos DB](./change-feed-functions.md), вы можете столкнуться с ситуацией, когда нужно выполнить несколько действий при появлении нового события в определенном [контейнере Azure Cosmos](./databases-containers-items.md#azure-cosmos-containers). Если действия, которые нужно активировать, не зависят друг от друга, лучше всего **создать по одному триггеру Cosmos DB на каждое действие**. Каждый из этих триггеров будет прослушивать изменения в одном и том же контейнере Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>Оптимизация контейнеров для нескольких триггеров

Учитывая *требования* к триггеру Cosmos DB, нам потребуется второй контейнер для хранения состояния, который называется *контейнером аренды*. Означает ли это, что для каждой функции Azure требуется отдельный контейнер аренды?

В этом случае существует два подхода.

* Создать **один контейнер аренды для каждой функции**. Такой подход может привести к дополнительным затратам, если вы не используете [базу данных с общей пропускной способностью](./set-throughput.md#set-throughput-on-a-database). Помните, что минимальное значение пропускной способности на уровне контейнера равно 400 [единиц запросов](./request-units.md), а при наличии контейнера аренды она используется только для установки контрольных точек выполнения и поддержания состояния.
* Создать **один контейнер аренды и совместно использовать его** для всех функций. При таком подходе подготовленные для контейнера единицы запроса будут использоваться более эффективно, так как это позволяет нескольким функциям Azure совместно использовать одну подготовленную пропускную способность.

В этой статье подробно описывается реализация второго подхода.

## <a name="configuring-a-shared-leases-container"></a>Настройка общего контейнера аренды

Чтобы настроить общий контейнер аренды, вам нужно выполнить с триггерами одно дополнительное действие — добавить [атрибут](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) `LeaseCollectionPrefix` при использовании C# или [атрибут](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example) `leaseCollectionPrefix` при использовании JavaScript. Значением атрибута должен быть логический дескриптор, описывающий, что собой представляет определенный триггер.

Например, у вас есть три триггера: один отправляет сообщения электронной почты, другой использует статистическую обработку для создания материализованного представления, а третий отправляет изменения в другое хранилище для последующего анализа. Вы можете назначить атрибуту `LeaseCollectionPrefix` значение emails для первого триггера, значение materialized для второго триггера, и значение analytics для третьего.

Важно то, что все три триггера **могут использовать одну и ту же конфигурацию контейнера аренды** (имя учетной записи, базы данных и контейнера).

Простой пример кода с использованием атрибута `LeaseCollectionPrefix` на C# будет выглядеть так:

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

Для JavaScript вы можете применить конфигурацию с атрибутом `leaseCollectionPrefix` к файлу `function.json`:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> Всегда отслеживайте использование единиц запросов, подготовленных на общем контейнере аренды. Каждый триггер, использующий этот контейнер, увеличит среднюю нагрузку на пропускную способность, поэтому при увеличении числа использующих ее функций Azure может потребоваться увеличить подготовленную пропускную способность.

## <a name="next-steps"></a>Дополнительная информация

* См. полную конфигурацию [триггера Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration).
* Ознакомьтесь с расширенным [списком примеров](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) для всех языков.
* Изучите другие примеры сценариев бессерверных вычислений с Функциями Azure и Azure Cosmos DB в [репозитории GitHub](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios).