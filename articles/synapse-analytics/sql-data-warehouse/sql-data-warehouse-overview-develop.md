---
title: Ресурсы для разработки пула Synapse S'L в azure Synapse Analytics
description: Концепции разработки, проектные решения, рекомендации и методы кодирования хранилища данных S'L.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411675"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Проектные решения и методы кодирования для пула Synapse S'L в Azure Synapse Analytics 
 В этой статье вы найдете дополнительные ресурсы, которые помогут вам лучше понять ключевые проектные решения, рекомендации и методы кодирования для пула S'L в Azure Synapse.

## <a name="key-design-decisions"></a>Основные проектные решения
В следующих статьях освещаются концепции и проектные решения по созданию распределенного хранилища данных с использованием возможностей пула S'L в Azure Synapse:

* [Соединения](../sql/connect-overview.md)
* [Параллелизма](resource-classes-for-workload-management.md)
* [Операций](sql-data-warehouse-develop-transactions.md)
* [пользовательские схемы](sql-data-warehouse-develop-user-defined-schemas.md)
* [распределение таблиц](sql-data-warehouse-tables-distribute.md);
* [индексы таблицы](sql-data-warehouse-tables-index.md);
* [разделы таблицы;](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [Статистика](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Рекомендации по разработке и методики программирования
В следующих статьях представлены конкретные методы кодирования, советы и рекомендации по разработке пула S'L:

* [сохраненные процедуры](sql-data-warehouse-develop-stored-procedures.md)
* [Метки](sql-data-warehouse-develop-label.md)
* [Представления](sql-data-warehouse-develop-views.md)
* [временные таблицы](sql-data-warehouse-tables-temporary.md);
* [динамичный СЗЛ](sql-data-warehouse-develop-dynamic-sql.md)
* [циклы](sql-data-warehouse-develop-loops.md);
* [группа по вариантам](sql-data-warehouse-develop-group-by-options.md)
* [переменная уступка](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительной справочной информации [см.](sql-data-warehouse-reference-tsql-statements.md)
