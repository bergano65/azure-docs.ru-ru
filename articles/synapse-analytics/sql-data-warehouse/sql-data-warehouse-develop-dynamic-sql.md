---
title: Использование динамического SQL
description: Советы по разработке решений с использованием динамического SQL в пуле синапсе SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bce79b8e18b3ec6f1fd139af280086281bbdda98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213471"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Динамический SQL в пуле синапсе SQL

В эту статью включены советы по разработке решений с использованием динамического SQL в пуле SQL.

## <a name="dynamic-sql-example"></a>Пример динамического SQL

При разработке кода приложения для пула SQL может потребоваться использовать динамический SQL, чтобы обеспечить гибкие, универсальные и модульные решения. В настоящее время пул SQL не поддерживает типы данных большого двоичного объекта.

Это может ограничивать размер строк, так как к двоичным относятся типы данных nvarchar(max) и varchar(max).

Если вы использовали эти типы в коде приложения для создания больших строк, необходимо разбить код на блоки и использовать вместо этого инструкцию EXEC.

Вот простой пример:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Если строка короткая, то можно использовать [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) как обычно.

> [!NOTE]
> Инструкции, выполняемые как динамические SQL, будут по-прежнему подвергаться всем правилам проверки T-SQL.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке приведены в [обзоре разработки](sql-data-warehouse-overview-develop.md).
