---
title: Ресурсы для разработки функций Synapse S'L
description: Концепции разработки, проектные решения, рекомендации и методы кодирования для Synapse S'L.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ba6ceec3064c5c876ca899ab58881e23913b9701
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429022"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Проектные решения и методы кодирования для функций Synapse S'L в Azure Synapse Analytics
В этой статье вы найдете список ресурсов для пула S'L и функций Synapse S'L по запросу. Рекомендуемые статьи разделены на два раздела: ключевые проектные решения и методы разработки и кодирования.

Целью этих статей является помощь в разработке оптимального технического подхода к компонентам Synapse S'L в Synapse Analytics.

## <a name="key-design-decisions"></a>Основные проектные решения
В приведенных ниже статьях излагается концепция и проектные решения для разработки Synapse S'L:

|                                                          |   Бассейн СЗЛ   | СЗЛ по запросу |
| -----------------------------------------------------    | ---- | ---- |
| [Подключения](connect-overview.md)                    | Да | Да |
| [Классы ресурсов и параллелизм](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Да    | нет |
| [Транзакции](develop-transactions.md)              | Да | нет |
| [Пользовательские схемы](develop-user-defined-schemas.md) | Да | Да |
| [Распределение таблицы](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Да | нет |
| [Таблицы индексов](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Да | нет |
| [Столовые перегородки](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Да | нет |
| [Статистика](develop-tables-statistics.md)            | Да | Да |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Да | нет |
| [Внешние таблицы](develop-tables-external-tables.md) | Да | Да |
| [CETAS](develop-tables-cetas.md)                     | Да | Да |


## <a name="recommendations"></a>Рекомендации

Ниже вы найдете основные статьи, которые подчеркивают конкретные методы кодирования, советы и рекомендации для развития:

|                                            | Бассейн СЗЛ | СЗЛ по запросу |
| ------------------------------------------ | ------------------ | ----------------------- |
| [Хранимые процедуры](develop-stored-procedures.md)  | Да                | нет                      |
| [Метки](develop-label.md)                           | Да                | нет                      |
| [Представления](develop-views.md)                             | Да                | Да                     |
| [Временные таблицы](develop-tables-temporary.md)       | Да                | Да                     |
| [Динамический SQL](develop-dynamic-sql.md)                 | Да                | Да                     |
| [Цикл](develop-loops.md)                         | Да                | Да                     |
| [Группировка по параметрам](develop-group-by-options.md)       | Да                | нет                      |
| [Присвоение значения переменной](develop-variable-assignment.md) | Да                | Да                     |

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительной справочной информации [см.](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

