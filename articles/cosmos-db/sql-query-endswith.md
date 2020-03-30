---
title: ENDSWITH на языке запросов Azure Cosmos DB
description: Узнайте о функции системы ENDSWITH в Azure Cosmos DB, чтобы вернуть Boolean, указывающий, заканчивается ли первое выражение строки вторым
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299454"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Космос DB)
 Возвращает значение логического типа, указывающее, заканчивается ли первое строковое выражение вторым.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr1*  
   Является выражением строки.  
  
*str_expr2*  
   Является ли строка выражение по сравнению с конца *str_expr1*.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  Пример ниже возвращает строки "abc", в конце которых есть "b" и "bc".  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Результирующий набор:  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строка функции Azure Космос DB](sql-query-string-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
