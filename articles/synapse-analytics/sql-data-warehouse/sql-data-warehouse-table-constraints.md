---
title: Первичные, иностранные и уникальные ключи
description: Поддержка ограничений таблицы в пуле Synapse S'L в Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 881e4f0110e3c0f35301e2ae6be40f2510f42539
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583491"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Первичный ключ, иностранный ключ и уникальный ключ в бассейне Synapse S'L

Узнайте об ограничениях таблиц в пуле Synapse S-L, включая основной ключ, иностранный ключ и уникальный ключ.

## <a name="table-constraints"></a>Ограничения таблиц

Бассейн Synapse S'L поддерживает следующие ограничения таблицы: 
- PRIMARY KEY поддерживается только тогда, когда используются NONCLUSTERED и NOT ENFORCED.    
- Ограничение УНИКУЕ поддерживается только с помощью NOT ENFORCED используется.

Ограничение FOREIGN KEY не поддерживается в пуле Synapse S'L.  

## <a name="remarks"></a>Примечания

Наличие основного ключа и/или уникального ключа позволяет движку пула Synapse s'L создать оптимальный план выполнения запроса.  Все значения в основной колонке ключа или уникальном столбце ограничения должны быть уникальными.

После создания таблицы с основным ключом или уникальным ограничением в пуле Synapse S'L пользователи должны убедиться, что все значения в этих столбцах уникальны.  Нарушение этого правила может привести к тому, что запрос вернет неточный результат.  Этот пример показывает, как запрос может вернуть неточный результат, если основной столбец ограничения ключа или уникальный элемент ограничения включает дублирующие значения.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Примеры

Создайте бильярдный стол Synapse S'L с основным ключом: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Создайте бильярдный стол Synapse S'L с уникальным ограничением:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Следующие шаги

После создания таблиц для пула Synapse S'L следующим шагом является загрузка данных в таблицу. Для руководства по [Loading data to Synapse SQL pool](load-data-wideworldimportersdw.md)загрузке см.
