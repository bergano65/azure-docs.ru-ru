---
title: ARRAY_CONTAINS на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System ARRAY_CONTAINS в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247956ccc2718c9bf192b4d704a48014753c00dc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348702"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Возвращает логическое значение, указывающее, содержит ли массив указанное значение. Вы можете проверить частичное или полное совпадение объекта с помощью логического выражение в команде. 

## <a name="syntax"></a>Синтаксис
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Аргументы
  
*arr_expr*  
   Выражение массива для поиска.  
  
*expr*  
   Выражение, которое должно быть найдено.  

*bool_expr*  
   Выражение типа Boolean. Если результатом вычисления является значение true, а для указанного значения поиска — объект, команда проверяет наличие частичного совпадения (объект поиска является подмножеством одного из объектов). Если значение равно false, команда проверяет полное совпадение всех объектов в массиве. Если не задано, по умолчанию используется значение false. 
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает логическое значение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как проверить членство в массиве с помощью `ARRAY_CONTAINS`.  
  
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
  

## <a name="next-steps"></a>Следующие шаги

- [Функции массива Azure Cosmos DB](sql-query-array-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
