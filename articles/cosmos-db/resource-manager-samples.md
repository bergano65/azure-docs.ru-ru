---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 14c3438c5d6e7bed110f9e0dbfcd04655c3e9121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193264"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Шаблоны Azure Resource Manager для Azure Cosmos DB

Следующие таблицы содержат ссылки на шаблоны Azure Resource Manager для Azure Cosmos DB.

## <a name="sql-core-api"></a>API SQL (Core)

|**Шаблон**|**Описание**|
|---|---|
|[Создание учетной записи, базы данных и контейнера Azure Cosmos DB](manage-sql-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись SQL (Core) API в двух регионах с двумя контейнерами с пропускной способностью общей базы данных и контейнером с выделенной пропускной способностью. Пропускную способность можно обновить, повторно отправив шаблон с обновленным значением свойства пропускной способности. |
|[Создание учетной записи Azure Cosmos, базы данных и контейнера с помощью хранимой процедуры, триггера и определяемой пользователем функции](manage-sql-with-resource-manager.md#create-sproc) | Этот шаблон создает учетную запись SQL (Core) API в двух регионах с хранимой процедурой, триггером и определяемой пользователем функцией для контейнера. |
|[Создание частной конечной точки для существующей учетной записи Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Этот шаблон создает закрытую конечную точку для существующей учетной записи API Azure Cosmos SQL в существующей виртуальной сети. |
|[Создание учетной записи Azure Cosmos уровня Free](manage-sql-with-resource-manager.md#free-tier) |  Этот шаблон создает учетную запись Azure Cosmos DB на уровне Free. |

## <a name="mongodb-api"></a>API MongoDB

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, базы данных, коллекции](manage-mongodb-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись с помощью Azure Cosmos DB API для MongoDB в двух регионах с поддержкой нескольких хозяев. Учетная запись Azure Cosmos будет содержать два контейнера, которые совместно используют пропускную способность на уровне базы данных. |

## <a name="cassandra-api"></a>API Cassandra

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, пространства ключей, Table](manage-cassandra-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись API Cassandra в двух регионах с включенной поддержкой нескольких хозяев. Учетная запись Azure Cosmos будет содержать две таблицы, которые совместно используют пропускную способность на уровне пространства ключей. |

## <a name="gremlin-api"></a>API Gremlin

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, базы данных, графа](manage-gremlin-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись API Gremlin в двух регионах с поддержкой нескольких хозяев. Учетная запись Azure Cosmos будет иметь два графика с общей пропускной способностью на уровне базы данных. |

## <a name="table-api"></a>API таблиц

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, таблицы](manage-table-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись API таблиц в двух регионах с включенной поддержкой нескольких хозяев. Учетная запись Azure Cosmos будет иметь одну таблицу. |

> [!TIP]
> Чтобы включить общую пропускную способность при использовании API таблиц, включите пропускную способность на уровне учетной записи на портале Azure.

Справочную документацию по Azure Cosmos DB странице см. в справочнике по [Azure Resource Manager](/azure/templates/microsoft.documentdb/allversions) .
