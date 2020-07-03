---
title: Язык запросов ROUND Azure Cosmos DB
description: Дополнительные сведения о функции SQL System ROUND в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6aac5a963d0f58a3b21b9fb0958793169a3d444
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302123"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
 Возвращает числовое значение, округленное до ближайшего целого значения в большую сторону.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="remarks"></a>Remarks
  
  Операция округления выполняется после среднего значения, округленного от нуля. Если входное значение является числовым выражением, которое находится в точности между двумя целыми числами, результатом будет ближайшее целое число от нуля.  
  
  |<numeric_expr>|Округляется|
  |-|-|
  |— 6,5000|-7|
  |— 0,5|-1|
  |0,5|1|
  |6,5000|7||
  
## <a name="examples"></a>Примеры
  
  В примере ниже положительные и отрицательные числа округляются до ближайшего целого.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Результирующий набор:  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция будет полезна из [индекса диапазона](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Дальнейшие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство с Azure Cosmos DB](introduction.md)
