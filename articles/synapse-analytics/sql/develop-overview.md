---
title: Ресурсы для разработки функций SQL синапсе
description: Концепции разработки, решения по проектированию, рекомендации и методы программирования для синапсе SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f4c61b43855e4a845956a937d9d9cfe738d6f47a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032951"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Разработка решений и методов программирования для функций синапсе SQL в Azure синапсе Analytics
В этой статье вы найдете список ресурсов для пула SQL и функций SQL по запросу (Предварительная версия) синапсе SQL. Рекомендуемые статьи делятся на два раздела: основные решения по проектированию и методики разработки и программирования.

Цель этих статей — помочь вам разработать оптимальный технический подход к синапсе компонентам SQL в синапсе Analytics.

## <a name="key-design-decisions"></a>Основные проектные решения
Приведенные ниже статьи выделяют основные понятия и архитектурные решения для синапсе разработки SQL.

| Статья | Пул SQL | SQL по запросу |
| ------- | -------- | ------------- |
| [Соединения](connect-overview.md)                    | Да | Да |
| [Классы и параллелизм ресурсов](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Да    | Нет |
| [Транзакции](develop-transactions.md)              | Да | Нет |
| [Пользовательские схемы](develop-user-defined-schemas.md) | Да | Да |
| [Distributing tables in SQL Data Warehouse (Распределение таблиц в хранилище данных SQL)](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Да | Нет |
| [Индексы таблиц](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Да | Нет |
| [Разделы таблиц](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Да | Нет |
| [Статистика](develop-tables-statistics.md)            | Да | Да |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Да | Нет |
| [Внешние таблицы](develop-tables-external-tables.md) | Да | Да |
| [CETAS](develop-tables-cetas.md)                     | Да | Да |


## <a name="recommendations"></a>Рекомендации

Ниже приводятся статьи, посвященные конкретным методам программирования, советам и рекомендациям по разработке.

| Статья | Пул SQL | SQL по запросу |
| ------- | -------- | ------------- |
| [Хранимые процедуры](develop-stored-procedures.md)  | Да                | Нет                      |
| [Метки](develop-label.md)                           | Да                | Нет                      |
| [Представления](develop-views.md)                             | Да                | Да                     |
| [Временные таблицы](develop-tables-temporary.md)       | Да                | Да                     |
| [Динамический SQL](develop-dynamic-sql.md)                 | Да                | Да                     |
| [Выполнение цикла](develop-loops.md)                         | Да                | Да                     |
| [Группировка по параметрам](develop-group-by-options.md)       | Да                | Нет                      |
| [Присваивание переменной](develop-variable-assignment.md) | Да                | Да                     |

## <a name="next-steps"></a>Дальнейшие шаги
Дополнительные справочные сведения см. в статье [инструкции T-SQL пула SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

