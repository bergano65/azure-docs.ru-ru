---
title: Используйте динамический СЗЛ в Synapse S'L
description: Советы по использованию динамического S'L в Synapse S'L.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: b546e6ba6967edcf41e2830a639e69d436827c40
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429607"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Динамический СЗЛ в Синапсе СЗЛ
В этой статье вы найдете советы по использованию динамического S'L и разработке решений с использованием Synapse S'L.

## <a name="dynamic-sql-example"></a>Пример динамического SQL

При разработке кода приложения может потребоваться использование динамического S'L для предоставления гибких, универсальных и модульных решений.

> [!NOTE]
> В настоящее время пул S'L не поддерживает типы данных blob. Это может ограничивать размер строк, так как к двоичным относятся типы данных nvarchar(max) и varchar(max). Если вы использовали эти типы в коде приложения при создании очень больших строк, необходимо будет разбить код на фрагменты и вместо этого использовать инструкцию EXEC.

Вот простой пример:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Если строка короткая, то можно использовать [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) как обычно.

> [!NOTE]
> Заявления, выполненные в качестве динамического S'L, по-прежнему будут подчиняться всем правилам проверки T-S'L.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](develop-overview.md).
