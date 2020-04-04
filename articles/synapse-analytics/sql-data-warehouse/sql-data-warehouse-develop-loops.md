---
title: Использование циклов Т-ЗЗл
description: Советы по разработке решений с использованием циклов T-S'L и замене курсоров в пуле Synapse S'L.
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
ms.openlocfilehash: 72a39804931c0834233e91190aacffa8d35912df
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633475"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Использование петли Т-СЗЛ в бассейне Synapse S'L

В эту статью включены советы по разработке решений для пула СЗЛ с использованием циклов T-S'L и замены курсоров.

## <a name="purpose-of-while-loops"></a>Назначение циклов WHILE

Бассейн Synapse S'L поддерживает цикл [WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) для многократного выполнения блоков оператора. Цикл WHILE продолжается, пока не будут выполнены указанные условия или пока код не прервет цикл с помощью ключевого слова BREAK.

Циклы полезны для замены курсоров, определенных в коде SQL. К счастью, почти все курсоры, записанные в коде SQL, относятся к разряду перемотки и доступности только для чтения. Таким образом, ПЕТли WHILE являются отличной альтернативой для замены курсоров.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Замена курсоров в бассейне Synapse S'L

Однако, прежде чем сначала нырять в голову, вы должны задать себе следующий вопрос: «Может ли этот курсор быть переписан для использования сет-операций?»

Во многих случаях ответ "да" и часто является наилучшим подходом. Операция, ориентированная на работу с набором данных, нередко выполняется быстрее, чем итеративный метод построчного перебора.

Курсоры быстрой перемотки, доступные только для чтения, легко заменяются циклической конструкцией. Следующий пример прост. Приведенный в примере код обновляет статистику для каждой таблицы в базе данных. Перебор таблиц в цикле позволяет выполнить каждую команду в последовательности.

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

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](sql-data-warehouse-overview-develop.md).
