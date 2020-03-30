---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB
description: Используйте шаблоны менеджера ресурсов Azure для создания и настройки DB Azure Cosmos.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961852"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Шаблоны Azure Resource Manager для Azure Cosmos DB

Следующие таблицы содержат ссылки на шаблоны управления ресурсами Azure для Azure Cosmos DB:

## <a name="sql-core-api"></a>API SQL (Core)

|**Шаблон**|**Описание**|
|---|---|
|[Создание учетной записи, базы данных и контейнера Azure Cosmos DB](manage-sql-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись API ВС-L (Core) в двух регионах с двумя контейнерами с общей пропускной емкостью базы данных и контейнером с выделенной пропускной способности. Пропускная часть может быть обновлена путем повторной отправки шаблона с обновленной стоимостью свойства пропускной способности. |
|[Создание учетной записи, базы данных и контейнера Azure Cosmos с сохраненной процедурой, триггером и UDF](manage-sql-with-resource-manager.md#create-sproc) | Этот шаблон создает учетную запись API ВС-L (Core) в двух регионах с сохраненной процедурой, триггером и UDF для контейнера. |

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
