---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961852"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Шаблоны Azure Resource Manager для Azure Cosmos DB

Следующие таблицы содержат ссылки на шаблоны Azure Resource Manager для Azure Cosmos DB.

## <a name="sql-core-api"></a>API SQL (Core)

|**Шаблон**|**Описание**|
|---|---|
|[Создание учетной записи Azure Cosmos, базы данных, контейнера](manage-sql-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись SQL (Core) API в двух регионах с двумя контейнерами с пропускной способностью общей базы данных и контейнером с выделенной пропускной способностью. Пропускную способность можно обновить, повторно отправив шаблон с обновленным значением свойства пропускной способности. |
|[Создание учетной записи Azure Cosmos, базы данных и контейнера с помощью хранимой процедуры, триггера и определяемой пользователем функции](manage-sql-with-resource-manager.md#create-sproc) | Этот шаблон создает учетную запись SQL (Core) API в двух регионах с хранимой процедурой, триггером и определяемой пользователем функцией для контейнера. |

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
