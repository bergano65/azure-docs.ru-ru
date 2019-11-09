---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: thvankra
ms.openlocfilehash: 7c508d736c7c04cc6df2b42e0a90a2906052adbc
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835515"
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
|[Обновление пропускной способности (единиц запросов/с) для базы данных](manage-mongodb-with-resource-manager.md#database-ru-update) | Этот шаблон обновляет пропускную способность для базы данных в учетной записи API MongoDB. |
|[Обновить пропускную способность (единиц запросов/с) для коллекции](manage-mongodb-with-resource-manager.md#collection-ru-update) | Этот шаблон обновляет пропускную способность для контейнера в учетной записи API MongoDB. |

## <a name="cassandra-api"></a>API Cassandra

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, пространства ключей, Table](manage-cassandra-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись API Cassandra в двух регионах с включенной поддержкой нескольких хозяев. Учетная запись Azure Cosmos будет содержать две таблицы, которые совместно используют пропускную способность на уровне пространства ключей. |

## <a name="gremlin-api"></a>API Gremlin

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, базы данных, графа](manage-gremlin-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись API Gremlin в двух регионах с поддержкой нескольких хозяев. Учетная запись Azure Cosmos будет иметь два графика с общей пропускной способностью на уровне базы данных. |
|[Обновление пропускной способности (единиц запросов/с) для базы данных](manage-gremlin-with-resource-manager.md#database-ru-update) | Этот шаблон обновляет пропускную способность для базы данных в учетной записи API Gremlin. |
|[Обновление пропускной способности (единиц запросов в секунду) для графика](manage-gremlin-with-resource-manager.md#graph-ru-update) | Этот шаблон обновляет пропускную способность графа в учетной записи API Gremlin. |

## <a name="table-api"></a>API таблицы

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, таблицы](manage-table-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись API таблиц в двух регионах с включенной поддержкой нескольких хозяев. Учетная запись Azure Cosmos будет иметь одну таблицу. |
|[Обновление пропускной способности (единиц запросов/с) для таблицы](manage-table-with-resource-manager.md#table-ru-update) | Этот шаблон обновляет пропускную способность для таблицы в учетной записи API таблиц. |

> [!TIP]
> Чтобы включить общую пропускную способность при использовании API таблиц, включите пропускную способность на уровне учетной записи на портале Azure.

Справочную документацию см. в [справочнике по ARM для Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) странице.
