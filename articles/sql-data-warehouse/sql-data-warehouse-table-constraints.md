---
title: Первичные, внешние и уникальные ключи
description: Поддержка табличных ограничений в аналитике SQL в Azure синапсе Analytics
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0379bed08c3ee6931e931a78a2d2c91664535250
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198139"
---
# <a name="primary-key-foreign-key-and-unique-key-in-sql-analytics"></a>Первичный ключ, внешний ключ и уникальный ключ в SQL Analytics

Сведения об ограничениях таблиц в SQL Analytics, включая первичный ключ, внешний ключ и уникальный ключ.

## <a name="table-constraints"></a>Ограничения таблиц 
SQL Analytics поддерживает следующие ограничения таблицы: 
- ПЕРВИЧный ключ поддерживается только в том случае, если используются некластеризованные и непринудительные.    
- Ограничение UNIQUE поддерживается только при использовании не применяется.   

Ограничение внешнего ключа не поддерживается в SQL Analytics.  

## <a name="remarks"></a>Remarks
Наличие первичного ключа и (или) уникального ключа позволяет подсистеме аналитики SQL создать оптимальный план выполнения для запроса.  Все значения в первичном ключевом столбце или столбце уникального ограничения должны быть уникальными. 

После создания таблицы с ограничением PRIMARY KEY или UNIQUE в SQL Analytics пользователи должны убедиться, что все значения в этих столбцах уникальны.  Нарушение этого запроса может привести к возврату неточного результата.  В этом примере показано, как запрос может вернуть неточный результат, если столбец первичного ключа или уникального ограничения содержит повторяющиеся значения.  

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
Создайте таблицу SQL Analytics с первичным ключом: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Создайте таблицу SQL Analytics с ограничением UNIQUE:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Дальнейшие действия

Следующим шагом после создания таблиц для базы данных аналитики SQL является загрузка данных в таблицу. Руководство по загрузке см. [в разделе Загрузка данных в базы данных SQL Analytics](load-data-wideworldimportersdw.md).
