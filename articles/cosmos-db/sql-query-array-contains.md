---
title: ARRAY_CONTAINS языка запросов Azure Cosmos DB
description: Сведения о том, как массив содержит системную функцию SQL в Azure Cosmos DB возвращает логическое значение, показывающее, содержит ли массив указанное значения.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 47fe20888aa546e414b268b30c2e03580750a040
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
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
   Выражение массива для поиска.  
  
*expr*  
   Выражение, которое должно быть найдено.  

*bool_expr*  
   Выражение типа Boolean. Если результатом вычисления является значение true, а для указанного значения поиска — объект, команда проверяет наличие частичного совпадения (объект поиска является подмножеством одного из объектов). Если значение равно false, команда проверяет полное совпадение всех объектов в массиве. Если не задано, по умолчанию используется значение false. 
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое значение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как проверить членство в массиве с помощью `ARRAY_CONTAINS` .  
  
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

Эта системная функция воспользуется преимуществами [индекса диапазона](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Дальнейшие действия

- [Функции массива Azure Cosmos DB](sql-query-array-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
