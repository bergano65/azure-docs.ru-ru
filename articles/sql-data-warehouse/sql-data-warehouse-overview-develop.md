---
title: Ресурсы для разработки хранилища данных в Azure синапсе Analytics
description: Основные понятия разработки, проектные решения, рекомендации и методики программирования для хранилища данных SQL.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 0f8967eab1be8351c50035d8afa784aa7afd79e1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153321"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Разработка решений и методов программирования для хранилища данных в Azure синапсе Analytics 
 В этой статье вы найдете дополнительные ресурсы, которые помогут вам лучше понять основные решения по проектированию, рекомендации и методы программирования для хранилища данных в Azure синапсе.

## <a name="key-design-decisions"></a>Основные проектные решения
В следующих статьях приводятся основные понятия и решения по проектированию для разработки распределенного хранилища данных с помощью возможности аналитики SQL в Azure синапсе:

* [подключения](sql-data-warehouse-connect-overview.md)
* [concurrency](resource-classes-for-workload-management.md)
* [транзакции;](sql-data-warehouse-develop-transactions.md)
* [пользовательские схемы;](sql-data-warehouse-develop-user-defined-schemas.md)
* [распределение таблиц;](sql-data-warehouse-tables-distribute.md)
* [индексы таблицы;](sql-data-warehouse-tables-index.md)
* [разделы таблицы;](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [статистика.](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Рекомендации по разработке и методики программирования
В следующих статьях приводятся специальные методы программирования, советы и рекомендации по разработке хранилища данных с помощью SQL Analytics.

* [хранимые процедуры;](sql-data-warehouse-develop-stored-procedures.md)
* [метки;](sql-data-warehouse-develop-label.md)
* [представления;](sql-data-warehouse-develop-views.md)
* [временные таблицы;](sql-data-warehouse-tables-temporary.md)
* [динамический SQL;](sql-data-warehouse-develop-dynamic-sql.md)
* [циклы;](sql-data-warehouse-develop-loops.md)
* [группировка по параметрам;](sql-data-warehouse-develop-group-by-options.md)
* [присваивание значения переменной.](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные справочные сведения см. в разделе [инструкции T-SQL](sql-data-warehouse-reference-tsql-statements.md).
