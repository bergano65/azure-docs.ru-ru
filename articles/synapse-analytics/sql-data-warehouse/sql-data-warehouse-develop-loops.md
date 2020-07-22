---
title: Использование циклов T-SQL
description: Советы по разработке решений с использованием циклов T-SQL и замене курсоров в синапсе SQL pool.
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
ms.openlocfilehash: 25dad01a54b6ffe08656379340f58e0fe70ec666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213420"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Использование циклов T-SQL в пуле SQL синапсе

В эту статью включены советы по разработке решений пула SQL с помощью циклов T-SQL и замены курсоров.

## <a name="purpose-of-while-loops"></a>Назначение циклов WHILE

Пул SQL синапсе поддерживает цикл [while](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) для многократного выполнения блоков инструкций. Цикл WHILE продолжается, пока не будут выполнены указанные условия или пока код не прервет цикл с помощью ключевого слова BREAK.

Циклы полезны для замены курсоров, определенных в коде SQL. К счастью, почти все курсоры, записанные в коде SQL, относятся к разряду перемотки и доступности только для чтения. Таким образом, циклы WHILE — отличная альтернатива замене курсоров.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Замена курсоров в пуле SQL синапсе

Однако прежде чем углубляться в голову, следует задать себе следующий вопрос: "можно ли переписывать этот курсор для использования операций на основе наборов?"

Во многих случаях ответ имеет значение Да и часто является лучшим подходом. Операция, ориентированная на работу с набором данных, нередко выполняется быстрее, чем итеративный метод построчного перебора.

Курсоры быстрой перемотки, доступные только для чтения, легко заменяются циклической конструкцией. Следующий пример является простым. Приведенный в примере код обновляет статистику для каждой таблицы в базе данных. Перебор таблиц в цикле позволяет выполнить каждую команду в последовательности.

Для начала создайте временную таблицу с уникальным номером строки, обозначающим отдельные операторы:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Затем инициализируйте переменные, необходимые для выполнения цикла:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Теперь запустите цикл последовательного выполнения операторов:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

И, наконец, удалите временную таблицу, созданной на первом этапе:

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке приведены в [обзоре разработки](sql-data-warehouse-overview-develop.md).
