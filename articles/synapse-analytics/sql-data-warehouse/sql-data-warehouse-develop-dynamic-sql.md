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
ms.openlocfilehash: 5a285c273a0bc590a9f5b4ade782f2195a361cd6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619036"
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

Если строка короткая, то можно использовать [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) как обычно.

> [!NOTE]
> Заявления, выполненные в качестве динамического S'L, по-прежнему будут подчиняться всем правилам проверки T-S'L.
> 
> 

## <a name="next-steps"></a>Следующие шаги
Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](sql-data-warehouse-overview-develop.md).

