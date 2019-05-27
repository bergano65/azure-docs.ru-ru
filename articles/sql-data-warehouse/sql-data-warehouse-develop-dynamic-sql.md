---
title: Применение динамического SQL в хранилище данных SQL Azure | Документация Майкрософт
description: Рекомендации по использованию динамического SQL в хранилище данных SQL для разработки решений.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 567637cab1c983992b08f65352ab9a92bd448c5a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861824"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Динамический SQL в хранилище данных SQL
Рекомендации по использованию динамического SQL в хранилище данных SQL для разработки решений.

## <a name="dynamic-sql-example"></a>Пример динамического SQL

При разработке кода приложения для хранилища данных SQL может возникнуть потребность в динамическом SQL для создания универсальных и гибких модульных решений. На данный момент хранилище данных SQL не поддерживает двоичные типы данных. Это может ограничивать размер строк, так как к двоичным относятся типы данных nvarchar(max) и varchar(max). Если вы использовали эти типы в коде приложения при создании очень больших строк, необходимо будет разбить код на фрагменты и вместо этого использовать инструкцию EXEC.

Вот простой пример:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Если строка короткая, то можно использовать [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) как обычно.

> [!NOTE]
> К инструкциям, выполняемым как динамический код SQL, по-прежнему будут применяться все правила проверки TSQL.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные советы по разработке приведены в [обзоре разработки](sql-data-warehouse-overview-develop.md).

