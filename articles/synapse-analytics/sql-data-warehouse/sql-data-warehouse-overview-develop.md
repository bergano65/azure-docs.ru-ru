---
title: Ресурсы для разработки пула SQL синапсе в Azure синапсе Analytics
description: Концепции разработки, решения по проектированию, рекомендации и методы программирования для Azure синапсе Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 95f712f196c37650b52220c9e34f6cb6b50bff23
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460615"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Разработка решений и методов программирования для пула синапсе SQL в Azure синапсе Analytics 
 В этой статье вы найдете дополнительные ресурсы, которые помогут вам лучше понять ключевые решения по проектированию, рекомендации и методы программирования для пула SQL в Azure синапсе.

## <a name="key-design-decisions"></a>Основные проектные решения
В следующих статьях приводятся основные понятия и решения по проектированию для разработки распределенного хранилища данных с помощью функции пула SQL в Azure синапсе:

* [соединение](../sql/connect-overview.md)
* [параллелизма](resource-classes-for-workload-management.md)
* [провод](sql-data-warehouse-develop-transactions.md)
* [определяемые пользователем схемы](sql-data-warehouse-develop-user-defined-schemas.md)
* [распределение таблиц](sql-data-warehouse-tables-distribute.md)
* [индексы таблицы](sql-data-warehouse-tables-index.md)
* [секции таблицы](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [статистически](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Рекомендации по разработке и методики программирования
В следующих статьях приводятся специальные методы программирования, советы и рекомендации по разработке пула SQL:

* [хранимые процедуры](sql-data-warehouse-develop-stored-procedures.md)
* [сигнатур](sql-data-warehouse-develop-label.md)
* [представления;](performance-tuning-materialized-views.md)
* [временные таблицы](sql-data-warehouse-tables-temporary.md)
* [динамический SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [циклов](sql-data-warehouse-develop-loops.md)
* [Параметры группировки](sql-data-warehouse-develop-group-by-options.md)
* [Присваивание переменной](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные справочные сведения см. в разделе [инструкции T-SQL](sql-data-warehouse-reference-tsql-statements.md).
