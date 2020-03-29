---
title: CEILING на языке запросов Azure Cosmos DB
description: Узнайте о том, как система CEILING S'L в Azure Cosmos DB возвращает наименьшее значение, превышающее или равное указанному численному выражению.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 81f113aa51a7f739b506ec7e3eb5bf2cb9f49a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302650"
---
# <a name="ceiling-azure-cosmos-db"></a>CEILING (Azure Космос DB)
 Возвращает наименьшее целочисленное значение, которое больше или равно указанному числовому выражению.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример показывает положительные числовые, отрицательные и нулевые значения с функцией. `CEILING`  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Результирующий набор:  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>Remarks

Эта функция системы будет пользоваться [индексом диапазона.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
