---
title: Использование циклов T-SQL
description: Советы по использованию циклов T-SQL, замене курсоров и разработке связанных решений с помощью пула SQL в синапсе SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ec15f87bdbf381b71faa7cefd986dd5e55e17a19
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964997"
---
# <a name="use-t-sql-loops-in-synapse-sql"></a>Использование циклов T-SQL в синапсе SQL
В этой статье приводятся ключевые советы по использованию циклов T-SQL, замене курсоров и разработке связанных решений с помощью пула SQL в синапсе SQL.

## <a name="purpose-of-while-loops"></a>Назначение циклов WHILE

Синапсе SQL поддерживает цикл [while](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) для многократного выполнения блоков инструкций. Цикл WHILE продолжается, пока не будут выполнены указанные условия или пока код не прервет цикл с помощью ключевого слова BREAK. 

Циклы в пуле SQL полезны для замены курсоров, определенных в коде SQL. К счастью, почти все курсоры, записанные в коде SQL, относятся к разряду перемотки и доступности только для чтения. Таким образом, циклы [WHILE] — отличная альтернатива замене курсоров.

## <a name="replacing-cursors-in-sql-pool"></a>Замена курсоров в пуле SQL

Прежде чем углубляться в, следует принять во внимание следующий вопрос: "можно ли переписывать этот курсор для использования операций на основе наборов?" Во многих случаях ответ имеет значение Да и часто является лучшим подходом. Операция, основанная на множестве, часто выполняется быстрее, чем методом итеративного, построчного по строкам.

Курсоры быстрого перемотки только на чтение легко заменяются циклической конструкцией. Ниже приведен простой пример кода. Приведенный в примере код обновляет статистику для каждой таблицы в базе данных. Перебор таблиц в цикле позволяет выполнить каждую команду в последовательности.

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

Во вторых, инициализируйте переменные, необходимые для выполнения цикла:

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

Дополнительные советы по разработке приведены в [обзоре разработки](develop-overview.md).
