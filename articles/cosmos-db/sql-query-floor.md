---
title: FLOOR на языке запросов Azure Cosmos DB
description: Узнайте о функции системы FLOOR S'L в Azure Cosmos DB, чтобы вернуть самый большой число ниже или равняться указанному численному выражению
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04dfa6a028cf7c44bf99c665b396d51d8a0f3cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303194"
---
# <a name="floor-azure-cosmos-db"></a>FLOOR (Azure Космос DB)
 Возвращает наибольшее целое число, меньшее или равное указанному числовому выражению.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример показывает положительные числовые, отрицательные и нулевые значения с функцией. `FLOOR`  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Результирующий набор:  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>Remarks

Эта функция системы будет пользоваться [индексом диапазона.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
