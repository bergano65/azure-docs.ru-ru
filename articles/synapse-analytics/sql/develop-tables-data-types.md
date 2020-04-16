---
title: Определение типов данных
description: Рекомендации по определению типов данных таблицы в Synapse S'L.
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
ms.openlocfilehash: 312c58a7df09ebe3e0bdf0749f902199723a331b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429009"
---
# <a name="table-data-types-in-synapse-sql"></a>Типы данных таблицы в Synapse S

Рекомендации по определению типов данных таблицы в Synapse S'L. 

## <a name="what-are-the-data-types"></a>Какие типы данных используются?

Синапсе S'L поддерживает наиболее часто используемые типы данных. Список поддерживаемых типов данных см. в [типах данных](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) в инструкции CREATE TABLE. 

## <a name="minimize-row-length"></a>Уменьшение длины строки

Уменьшение размера типов данных сокращает длину строки, что улучшает производительность запросов. Используйте наименьший тип данных для данных.

- Не рекомендуется использовать по умолчанию длинные значения столбцов. Например, если самое длинное значение состоит из 25 знаков, столбец необходимо определить как VARCHAR(25).
- Не нужно использовать [NVARCHAR][NVARCHAR], если вам требуется только VARCHAR.
- По возможности используйте NVARCHAR(4000) или VARCHAR(8000) вместо NVARCHAR(MAX) или VARCHAR(MAX).

> [!NOTE]
> Если для загрузки таблиц ы пула PolyBase используется внешние таблицы PolyBase, то определенная длина строки таблицы не может превышать 1 МБ. Если строка с данными переменной длины превышает 1 МБ, можно загрузить строку с помощью BCP, а не PolyBase.

## <a name="identify-unsupported-data-types"></a>Определение неподдерживаемых типов данных

Если вы мигрируете базой данных из другой базы данных S'L, вы можете столкнуться с типами данных, которые не поддерживаются в Synapse S'L. Используйте этот запрос для определения неподдерживаемых типов данных в существующей схеме SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Обходные решения для неподдерживаемых типов данных

В следующем списке показаны типы данных, которые Synapse S'L не поддерживает, и предоставляет альтернативы, которые можно использовать вместо неподдерживаемых типов данных.

| Неподдерживаемые типы данных | Обходной путь |
| --- | --- |
| [Геометрии](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [География](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Разделите столбец на несколько строго типизированных столбцов. |
| [table](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |При использовании пула S'L можно преобразовать их во временные таблицы. При использовании S'L (предварительный просмотр) можно рассмотреть возможность хранения данных для хранения с помощью [CETAS.](../sql/develop-tables-cetas.md) |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Для использования [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) и функции [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) внесите изменения в код. По умолчанию поддерживаются только константы, поэтому нельзя использовать current_timestamp как ограничение по умолчанию. Если нужно перенести значения версии строки из типизированного столбца timestamp, используйте [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) или [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) в качестве значений версии строки NOT NULL или NULL. |
| [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [определяемый пользователем тип](/sql/relational-databases/native-client/features/using-user-defined-types) |По возможности выполните преобразование в исходный тип данных. |
| Значения по умолчанию | Значения по умолчанию поддерживают только литералы и константы. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о разработке таблиц см. в статье [Общие сведения о проектировании таблиц в хранилище данных SQL Azure](develop-overview.md).
