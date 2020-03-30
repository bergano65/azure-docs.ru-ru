---
title: Первичные, иностранные и уникальные ключи
description: Ограничения таблиц поддержки в аналитике S'L в Azure Synapse Analytics
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
ms.openlocfilehash: b9336a5e230e90e1abd7f2d40d431b988385c009
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350032"
---
# <a name="primary-key-foreign-key-and-unique-key-in-sql-analytics"></a>Первичный ключ, иностранный ключ и уникальный ключ в s'L Analytics

Узнайте об ограничениях таблицы в S'L Analytics, включая основной ключ, иностранный ключ и уникальный ключ.

## <a name="table-constraints"></a>Ограничения таблиц 
Аналитика S'L поддерживает следующие ограничения таблицы: 
- PRIMARY KEY поддерживается только тогда, когда используются NONCLUSTERED и NOT ENFORCED.    
- Ограничение УНИКУЕ поддерживается только с помощью NOT ENFORCED используется.   

Ограничение FOREIGN KEY не поддерживается в s'L Analytics.  

## <a name="remarks"></a>Remarks
Наличие основного ключа и/или уникального ключа позволяет движку S'L Analytics создать оптимальный план выполнения запроса.  Все значения в основной колонке ключа или уникальном столбце ограничения должны быть уникальными. 

После создания таблицы с основным ключом или уникальным ограничением в S'L Analytics пользователи должны убедиться, что все значения в этих столбцах уникальны.  Нарушение этого правила может привести к тому, что запрос вернет неточный результат.  Этот пример показывает, как запрос может вернуть неточный результат, если основной столбец ограничения ключа или уникальный элемент ограничения включает дублирующие значения.  

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
Создайте таблицу аналитики СЗЛ с основным ключом: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Создайте таблицу аналитики S'L с уникальным ограничением:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Дальнейшие действия

После создания таблиц для базы данных S'L Analytics следующим шагом является загрузка данных в таблицу. Для руководства по [Loading data to SQL Analytics databases](load-data-wideworldimportersdw.md)загрузке см.
