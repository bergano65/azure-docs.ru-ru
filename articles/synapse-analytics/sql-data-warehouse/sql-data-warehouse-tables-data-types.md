---
title: Определение типов данных
description: Рекомендации по определению типов данных таблицы в пуле Synapse S'L.
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
ms.openlocfilehash: 5b3d24232c26bb4d483d360eb593bc361190ccfe
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631238"
---
# <a name="table-data-types-in-synapse-sql-pool"></a>Типы данных таблицы в пуле Synapse S'L
В эту статью включены рекомендации по определению типов таблицных данных в пуле S'L. 

## <a name="supported-data-types"></a>Поддерживаемые типы данных

Пулс S'L поддерживает наиболее часто используемые типы данных. Список поддерживаемых типов данных см. в [типах данных](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) в инструкции CREATE TABLE. 

## <a name="minimize-row-length"></a>Уменьшение длины строки
Уменьшение размера типов данных сокращает длину строки, что улучшает производительность запросов. Используйте наименьший тип данных для данных. 

- Не рекомендуется использовать по умолчанию длинные значения столбцов. Например, если самое длинное значение состоит из 25 знаков, столбец необходимо определить как VARCHAR(25). 
- Не нужно использовать [NVARCHAR](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql), если вам требуется только VARCHAR.
- По возможности используйте NVARCHAR(4000) или VARCHAR(8000) вместо NVARCHAR(MAX) или VARCHAR(MAX).

Если для загрузки таблиц используются внешние таблицы PolyBase, то определенная длина строки таблицы не может превышать 1 МБ. Если строка с данными переменной длины превышает 1 МБ, можно загрузить строку с помощью BCP, а не PolyBase.

## <a name="identify-unsupported-data-types"></a>Определение неподдерживаемых типов данных
Если вы мигрируете базисную базу данных из другой базы данных S'L, вы можете найти типы данных, которые не поддерживаются в пуле S'L. Используйте следующий запрос, чтобы обнаружить неподдерживаемые типы данных в существующей схеме S'L:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Обходные решения для неподдерживаемых типов данных

В следующем списке показаны типы данных, которые пул S'L не поддерживает, и предоставляет полезные альтернативы для неподдерживаемых типов данных.

| Неподдерживаемые типы данных | Обходной путь |
| --- | --- |
| [Геометрии](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [География](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [Изображение](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[Varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [Ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [Sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Разделите столбец на несколько строго типизированных столбцов. |
| [Таблице](/sql/t-sql/data-types/table-transact-sql) |Преобразуйте во временные таблицы. |
| [Timestamp](/sql/t-sql/data-types/date-and-time-types) |Для использования [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) и функции [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) внесите изменения в код. Только константы поддерживаются как по умолчанию, поэтому current_timestamp не может быть определенкак как ограничение по умолчанию. Если необходимо перенести значения строки версии из набранной колонки метки времени, используйте [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) или [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) для значений не NULL или NULL строки строки. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[Varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [определяемый пользователем тип](/sql/relational-databases/native-client/features/using-user-defined-types) |По возможности выполните преобразование в исходный тип данных. |
| Значения по умолчанию | Значения по умолчанию поддерживают только литералы и константы. |


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о разработке таблиц см. в статье [Общие сведения о проектировании таблиц в хранилище данных SQL Azure](sql-data-warehouse-tables-overview.md).
