---
title: Ресурсы для разработки пула SQL синапсе в Azure синапсе Analytics
description: Концепции разработки, решения по проектированию, рекомендации и приемы программирования для хранилища данных SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 12f91633834ee98582eaad886b48eb8619378265
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81411675"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Разработка решений и методов программирования для пула синапсе SQL в Azure синапсе Analytics 
 В этой статье вы найдете дополнительные ресурсы, которые помогут вам лучше понять ключевые решения по проектированию, рекомендации и методы программирования для пула SQL в Azure синапсе.

## <a name="key-design-decisions"></a>Основные проектные решения
В следующих статьях приводятся основные понятия и решения по проектированию для разработки распределенного хранилища данных с помощью функции пула SQL в Azure синапсе:

* [соединение](../sql/connect-overview.md)
* [параллелизма](resource-classes-for-workload-management.md)
* [провод](sql-data-warehouse-develop-transactions.md)
* [определяемые пользователем схемы](sql-data-warehouse-develop-user-defined-schemas.md)
* [распределение таблиц](sql-data-warehouse-tables-distribute.md);
* [индексы таблицы](sql-data-warehouse-tables-index.md);
* [разделы таблицы;](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [статистически](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Рекомендации по разработке и методики программирования
В следующих статьях приводятся специальные методы программирования, советы и рекомендации по разработке пула SQL:

* [хранимые процедуры](sql-data-warehouse-develop-stored-procedures.md)
* [сигнатур](sql-data-warehouse-develop-label.md)
* [представления;](sql-data-warehouse-develop-views.md)
* [временные таблицы](sql-data-warehouse-tables-temporary.md)
* [динамический SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [циклы](sql-data-warehouse-develop-loops.md);
* [Параметры группировки](sql-data-warehouse-develop-group-by-options.md)
* [Присваивание переменной](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Дальнейшие шаги
Дополнительные справочные сведения см. в разделе [инструкции T-SQL](sql-data-warehouse-reference-tsql-statements.md).
