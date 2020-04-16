---
title: Использование циклов Т-ЗЗл
description: Советы по использованию циклов Т-ЗЗЛ, замене курсоров и разработке связанных с ними решений с пулом S'L в Synapse S'L.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: baff2806b1a8c3c99546365c2496238c24b2b243
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429958"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>Использование петлей Т-СЗЛ в Synapse S'L
В этой статье вам предоставляются важные советы по использованию циклов T-S'L, замене курсоров и разработке связанных с ними решений с пулом S'L в Synapse S'L.

## <a name="purpose-of-while-loops"></a>Назначение циклов WHILE

Синапсе S'L поддерживает цикл [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) для многократного выполнения блоков оператора. Цикл WHILE продолжается, пока не будут выполнены указанные условия или пока код не прервет цикл с помощью ключевого слова BREAK. 

Циклы в пуле S'L полезны для замены курсоров, определенных в коде S'L. К счастью, почти все курсоры, записанные в коде SQL, относятся к разряду перемотки и доступности только для чтения. Таким образом, петли «WHILE» являются отличной альтернативой для замены курсоров.

## <a name="replacing-cursors-in-sql-pool"></a>Замена курсоров в пуле S'L

Перед погружением следует рассмотреть следующий вопрос: «Может ли этот курсор быть переписан для использования сет-операций?» Во многих случаях ответ "да" и часто является наилучшим подходом. Операция на основе набора часто выполняется быстрее, чем итеративный подход строки за строкой.

Курсоры перемотка вперед только для чтения легко заменяются циклом конструкции. Следующий код является простым примером. Приведенный в примере код обновляет статистику для каждой таблицы в базе данных. Перебор таблиц в цикле позволяет выполнить каждую команду в последовательности.

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

Во-вторых, инициализация переменных, необходимых для выполнения цикла:

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

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](develop-overview.md).
