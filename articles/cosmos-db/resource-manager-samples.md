---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969183"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Шаблоны Azure Resource Manager для Azure Cosmos DB

В таблице ниже перечислены ссылки на шаблоны Azure Resource Manager для Azure Cosmos DB:

## <a name="sql-core-api"></a>API SQL (Core)

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, база данных, контейнера](manage-sql-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись API SQL (ядро) в двух регионах с несколькими хозяевами включена. Учетная запись Azure Cosmos будет иметь два контейнера, которые совместно используют пропускную способность на уровне базы данных. |
|[Обновить пропускную способность (ЕЗ/с) для базы данных](manage-sql-with-resource-manager.md#database-ru-update) | Этот шаблон обновляет пропускной способности базы данных в учетной записи API SQL (ядро). |
|[Обновить пропускную способность (ЕЗ/с) для контейнера](manage-sql-with-resource-manager.md#container-ru-update) | Этот шаблон обновляет пропускную способность для контейнера в учетной записи API SQL (ядро). |

## <a name="mongodb-api"></a>API MongoDB

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, база данных, коллекции](manage-mongodb-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись с помощью Azure Cosmos DB API для MongoDB в двух регионах с несколькими хозяевами включена. Учетная запись Azure Cosmos будет иметь два контейнера, которые совместно используют пропускную способность на уровне базы данных. |
|[Обновить пропускную способность (ЕЗ/с) для базы данных](manage-mongodb-with-resource-manager.md#database-ru-update) | Этот шаблон обновляет пропускной способности базы данных в учетной записи MongoDB API. |
|[Обновить пропускную способность (ЕЗ/с) для коллекции](manage-mongodb-with-resource-manager.md#collection-ru-update) | Этот шаблон обновляет пропускную способность для контейнера в учетной записи MongoDB API. |

## <a name="cassandra-api"></a>API Cassandra

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, пространство ключей, таблица](manage-cassandra-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись Cassandra API в двух регионах с несколькими хозяевами включена. Учетная запись Azure Cosmos будет иметь две таблицы, которые совместно используют пространство ключей уровня пропускной способности. |
|[Обновить пропускную способность (ЕЗ/с) для пространства ключей](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Этот шаблон обновляет пропускную способность для ключей в учетной записи Cassandra API. |
|[Обновить пропускную способность (ЕЗ/с) для таблицы](manage-cassandra-with-resource-manager.md#table-ru-update) | Этот шаблон обновляет пропускную способность для таблицы в учетной записи Cassandra API. |

## <a name="gremlin-api"></a>API Gremlin

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, база данных, graph](manage-gremlin-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись Gremlin API в двух регионах с несколькими хозяевами включена. Учетная запись Azure Cosmos будет иметь два графика, которые совместно используют пропускную способность на уровне базы данных. |
|[Обновить пропускную способность (ЕЗ/с) для базы данных](manage-gremlin-with-resource-manager.md#database-ru-update) | Этот шаблон обновляет пропускной способности базы данных в учетной записи Gremlin API. |
|[Обновить пропускную способность (ЕЗ/с) для графа](manage-gremlin-with-resource-manager.md#graph-ru-update) | Этот шаблон обновляет пропускную способность для графа в учетной записи Gremlin API. |

## <a name="table-api"></a>API таблиц

|**Шаблон**|**Описание**|
|---| ---|
|[Создание учетной записи Azure Cosmos, таблица](manage-table-with-resource-manager.md#create-resource) | Этот шаблон создает учетную запись API таблиц в двух регионах с несколькими хозяевами включена. Учетная запись Azure Cosmos будет иметь одну таблицу. |
|[Обновить пропускную способность (ЕЗ/с) для таблицы](manage-table-with-resource-manager.md#table-ru-update) | Этот шаблон обновляет пропускную способность для таблицы в учетной записи API таблиц. |

> [!TIP]
> Чтобы включить общей пропускной способности, при использовании API таблиц, включите пропускной способности уровня учетной записи на портале Azure.

См. в разделе [ссылку ARM для Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) страницы справочную документацию.