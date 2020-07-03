---
title: ARRAY_CONCAT языка запросов Azure Cosmos DB
description: Узнайте, как системная функция Concat массива SQL в Azure Cosmos DB возвращает массив, являющийся результатом сцепления двух или более значений массива.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78295884"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Возвращает массив, который является результатом объединения значений двух или более массивов.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Аргументы
  
*arr_expr*  
   Выражение массива для сцепления с другими значениями. `ARRAY_CONCAT` Функции требуется по крайней мере два аргумента *arr_expr* .  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает выражение массива.  
  
## <a name="examples"></a>Примеры
  
  В примере ниже показано, как объединить два массива.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Результирующий набор:  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Функции массива Azure Cosmos DB](sql-query-array-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство с Azure Cosmos DB](introduction.md)
