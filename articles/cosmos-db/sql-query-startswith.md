---
title: STARTSWITH на языке запросов Azure Cosmos DB
description: Узнайте о функции системы S'L STARTSWITH в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9ed49c067946186f8b79f67bad0a460113eacb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295714"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Космос DB)
 Возвращает значение логического типа, указывающее, начинается ли первое строковое выражение вторым.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr1*  
   Является выражением строки.
  
*str_expr2*  
   Является ли строка выражение по сравнению с началом *str_expr1*.

## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  Пример ниже проверяет, начинается ли строка "abc" с "b" и "a".  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Результирующий набор:  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="remarks"></a>Remarks

Эта функция системы будет пользоваться [индексом диапазона.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Дальнейшие действия

- [Строка функции Azure Космос DB](sql-query-string-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
