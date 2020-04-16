---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB
description: Используйте шаблоны менеджера ресурсов Azure для создания и настройки DB Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 1c4f1a1920d98052231eaa3a7d5c0454441c88ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390879"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Шаблоны Azure Resource Manager для Azure Cosmos DB

Следующие таблицы содержат ссылки на шаблоны управления ресурсами Azure для Azure Cosmos DB:

## <a name="sql-core-api"></a>API SQL (Core)

|**Шаблон**|**Описание**|
|---|---|
|[Создание учетной записи, базы данных и контейнера Azure Cosmos DB](manage-sql-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись API ВС-L (Core) в двух регионах с двумя контейнерами с общей пропускной емкостью базы данных и контейнером с выделенной пропускной способности. Пропускная часть может быть обновлена путем повторной отправки шаблона с обновленной стоимостью свойства пропускной способности. |
|[Создание учетной записи, базы данных и контейнера Azure Cosmos с сохраненной процедурой, триггером и UDF](manage-sql-with-resource-manager.md#create-sproc) | Этот шаблон создает учетную запись API ВС-L (Core) в двух регионах с сохраненной процедурой, триггером и UDF для контейнера. |
|[Создание частной конечной точки для существующей учетной записи Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Этот шаблон создает частную конечную точку для существующей учетной записи Azure Cosmos S'L API в существующей виртуальной сети. |

## <a name="mongodb-api"></a>API MongoDB

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, базы данных, коллекции](manage-mongodb-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись с помощью API Azure Cosmos DB для MongoDB в двух регионах с включенным мультимастером. Учетная запись Azure Cosmos будет иметь два контейнера, которые разделяют пропускную емкость уровня базы данных. |

## <a name="cassandra-api"></a>API Cassandra

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, ключевого пространства, таблицы](manage-cassandra-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись Cassandra API в двух регионах с включенным мультимастером. Учетная запись Azure Cosmos будет иметь две таблицы, которые разделяют пропускную связь на уровне ключей. |

## <a name="gremlin-api"></a>API Gremlin

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, базы данных, графика](manage-gremlin-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись Gremlin API в двух регионах с включенным мультимастером. Учетная запись Azure Cosmos будет иметь два графика, которые разделяют пропускную связь на уровне базы данных. |

## <a name="table-api"></a>API таблиц

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, таблицы](manage-table-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись Table API в двух регионах с включенным мультимастером. Учетная запись Azure Cosmos будет иметь единую таблицу. |

> [!TIP]
> Для обеспечения общей пропускной записи при использовании Table API включите пропускную связь на уровне учетной записи в azure Portal.

Для справочной документации смотрите ссылку менеджера ресурсов Azure на страницу [Azure Cosmos DB.](/azure/templates/microsoft.documentdb/allversions)
