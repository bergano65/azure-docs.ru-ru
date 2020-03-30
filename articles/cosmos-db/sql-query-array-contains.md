---
title: ARRAY_CONTAINS на языке запросов Запроса Azure Cosmos DB
description: Узнайте о том, как функция системы Array Contains S'L в Azure Cosmos DB возвращает Boolean, указывая, содержит ли массив указанное значение
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 47fe20888aa546e414b268b30c2e03580750a040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303483"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Возвращает логическое значение, указывающее, содержит ли массив указанное значение. Вы можете проверить частичное или полное совпадение объекта с помощью логического выражение в команде. 

## <a name="syntax"></a>Синтаксис
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Аргументы
  
*arr_expr*  
   Является ли выражение массива для поиска.  
  
*expr*  
   Это выражение, чтобы быть найденным.  

*bool_expr*  
   Это булеан выражение. Если он оценивается как "истинный" и если указанное значение поиска является объектом, команда проверяет частичное соответствие (объект поиска является подмножеством одного из объектов). Если он оценивается как "ложный", команда проверяет полное соответствие всех объектов в массиве. Если не задано, по умолчанию используется значение false. 
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое значение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример, как проверить наличие `ARRAY_CONTAINS`членства в массиве с помощью .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Результирующий набор:  
  
```json
[{"b1": true, "b2": false}]  
```  

В примере ниже показано, как проверить частичное совпадение JSON в массиве с помощью функции ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Результирующий набор:  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Remarks

Эта функция системы будет пользоваться [индексом диапазона.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Дальнейшие действия

- [Array функции Azure Космос DB](sql-query-array-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
