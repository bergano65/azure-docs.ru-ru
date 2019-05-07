---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078465"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Шаблоны Azure Resource Manager для Azure Cosmos DB

Следующая таблица содержит ссылки на шаблоны Azure Resource Manager для Azure Cosmos DB:

|**Тип API.** | **ссылка на образец**| **Описание** |
|---|---| ---|
|API ядра (SQL)| [Создание учетной записи Azure Cosmos DB (с несколькими хозяевами)](manage-sql-with-resource-manager.md) | Этот шаблон создает учетную запись SQL API в двух регионах с несколькими хозяевами включена. Учетная запись Azure Cosmos будет иметь два контейнера, которые совместно используют пропускную способность на уровне базы данных. |
| API MongoDB | [Создание учетной записи Azure Cosmos DB (с несколькими хозяевами)](manage-mongodb-with-resource-manager.md) | Этот шаблон создает учетную запись с помощью API Azure Cosmos DB для MongoDB в двух регионах с несколькими хозяевами включена. Учетная запись Azure Cosmos будет иметь два контейнера, которые совместно используют пропускную способность на уровне базы данных. |
| API Cassandra | [Создание учетной записи Azure Cosmos DB (с несколькими хозяевами)](manage-cassandra-with-resource-manager.md) | Этот шаблон создает учетную запись Cassandra API в двух регионах с несколькими хозяевами включена. Учетная запись Azure Cosmos будет иметь две таблицы, которые совместно используют пространство ключей уровня пропускной способности. |
| API Gremlin| [Создание учетной записи Azure Cosmos DB (с несколькими хозяевами)](manage-gremlin-with-resource-manager.md) | Этот шаблон создает учетную запись Gremlin API в двух регионах с несколькими хозяевами включена. Учетная запись Azure Cosmos будет иметь два графика, которые совместно используют пропускную способность на уровне базы данных. |
| API таблицы | [Создание учетной записи Azure Cosmos DB (с несколькими хозяевами)](manage-table-with-resource-manager.md) | Этот шаблон создает учетную запись API таблиц в двух регионах с несколькими хозяевами включена. Учетная запись Azure Cosmos будет иметь одну таблицу. |

> [!TIP]
> Чтобы включить общей пропускной способности, при использовании API таблиц, включите пропускной способности уровня учетной записи на портале Azure.

См. в разделе [ссылку ARM для Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) страницы справочную документацию.