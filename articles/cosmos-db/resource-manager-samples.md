---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 501065875cafc035d491e606c016f3ad4dbfc5d4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791635"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Шаблоны Azure Resource Manager для Azure Cosmos DB

Следующие таблицы содержат ссылки на шаблоны Azure Resource Manager для Azure Cosmos DB.

## <a name="core-sql-api"></a>API Core (SQL)

|**Шаблон**|**Описание**|
|---|---|
|[Создание учетной записи Azure Cosmos, базы данных, контейнера с пропускной способностью автомасштабирования](manage-sql-with-resource-manager.md#create-autoscale) | Этот шаблон создает учетную запись Core (SQL) API в двух регионах: в базе данных и контейнере с пропускной способностью автомасштабирования. |
|[Создание учетной записи Azure Cosmos, базы данных, контейнера с пропускной способностью Standard (вручную)](manage-sql-with-resource-manager.md#create-manual) | Этот шаблон создает учетную запись Core (SQL) API в двух регионах: в базе данных и контейнере со стандартной пропускной способностью. |
|[Создание учетной записи Azure Cosmos, базы данных и контейнера с помощью хранимой процедуры, триггера и определяемой пользователем функции](manage-sql-with-resource-manager.md#create-sproc) | Этот шаблон создает учетную запись Core (SQL) API в двух регионах с хранимой процедурой, триггером и определяемой пользователем функцией для контейнера. |
|[Создание частной конечной точки для существующей учетной записи Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Этот шаблон создает закрытую конечную точку для существующей учетной записи API Azure Cosmos Core (SQL) в существующей виртуальной сети. |
|[Создание учетной записи Azure Cosmos уровня Free](manage-sql-with-resource-manager.md#free-tier) |  Этот шаблон создает учетную запись API Azure Cosmos DB Core (SQL) на уровне Free. |

## <a name="mongodb-api"></a>API MongoDB

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, базы данных, коллекции с пропускной способностью автомасштабирования](manage-mongodb-with-resource-manager.md#create-autoscale) | Этот шаблон создает учетную запись с помощью Azure Cosmos DB API для MongoDB в двух регионах с двумя контейнерами, которые совместно используют пропускную способность автомасштабирования на уровне базы данных. |
|[Создание учетной записи Azure Cosmos, базы данных, коллекции с пропускной способностью Standard (вручную)](manage-mongodb-with-resource-manager.md#create-manual) | Этот шаблон создает учетную запись с помощью Azure Cosmos DB API для MongoDB в двух регионах с двумя контейнерами, использующими стандартную пропускную способность уровня базы данных. |

## <a name="cassandra-api"></a>API Cassandra

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, пространства ключей, таблицы с пропускной способностью автомасштабирования](manage-cassandra-with-resource-manager.md#create-autoscale) | Этот шаблон создает учетную запись API Cassandra в двух регионах с пространства ключей и таблицей с пропускной способностью автомасштабирования. |
|[Создание учетной записи Azure Cosmos, пространства ключей, таблицы с пропускной способностью Standard (вручную)](manage-cassandra-with-resource-manager.md#create-manual) | Этот шаблон создает учетную запись API Cassandra в двух регионах с пространства ключей и таблицей с пропускной способностью вручную. |

## <a name="gremlin-api"></a>API Gremlin

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, базы данных, графа с пропускной способностью автомасштабирования](manage-gremlin-with-resource-manager.md#create-autoscale) | Этот шаблон создает учетную запись API Gremlin в двух регионах с базой данных и графом с пропускной способностью автомасштабирования. |
|[Создание учетной записи Azure Cosmos, базы данных, графа с обычной пропускной способностью (вручную)](manage-gremlin-with-resource-manager.md#create-manual) | Этот шаблон создает учетную запись API Gremlin в двух регионах с базой данных и графом со стандартной пропускной способностью. |

## <a name="table-api"></a>API таблиц

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, таблицы с пропускной способностью автомасштабирования](manage-table-with-resource-manager.md#create-autoscale) | Этот шаблон создает учетную запись API таблиц в двух регионах и одну таблицу с пропускной способностью автомасштабирования. |
|[Создание учетной записи Azure Cosmos, таблицы со стандартной пропускной способностью (вручную)](manage-table-with-resource-manager.md#create-manual) | Этот шаблон создает учетную запись API таблиц в двух регионах и одну таблицу со стандартной пропускной способностью. |

Справочную документацию по Azure Cosmos DB странице см. в справочнике по [Azure Resource Manager](/azure/templates/microsoft.documentdb/allversions) .
