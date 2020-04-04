---
title: Использование динамического S'L
description: Советы по разработке решений, использующих динамические S'L в бассейне Synapse S'L.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9280bb8153204f86096cf8249ff053bee3f71cc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633530"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Динамический S'L в бассейне Synapse S'L

В эту статью включены советы по решениям для разработки с использованием динамического S'L в пуле S'L.

## <a name="dynamic-sql-example"></a>Пример динамического SQL

При разработке кода приложения для пула S'L может потребоваться использование динамического S'L для предоставления гибких, универсальных и модульных решений. В настоящее время пул S'L не поддерживает типы данных blob.

Это может ограничивать размер строк, так как к двоичным относятся типы данных nvarchar(max) и varchar(max).

Если вы использовали эти типы в коде приложения для создания больших строк, необходимо разбить код на куски и вместо этого использовать выписку EXEC.

Вот простой пример:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Если строка короткая, то можно использовать [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) как обычно.

> [!NOTE]
> Заявления, выполненные в качестве динамического S'L, по-прежнему будут подчиняться всем правилам проверки T-S'L.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](sql-data-warehouse-overview-develop.md).
