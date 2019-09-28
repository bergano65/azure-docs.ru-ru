---
title: Язык запросов ROUND Azure Cosmos DB
description: Дополнительные сведения о функции SQL System ROUND в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4bad140ea1c6b1c59c8f752bc5336bbd49952e16
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349515"
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
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="remarks"></a>Примечания
  
  Операция округления выполняется после среднего значения, округленного от нуля. Если входное значение является числовым выражением, которое находится в точности между двумя целыми числами, результатом будет ближайшее целое число от нуля.  
  
  |< numeric_expr >|Округлено|
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

## <a name="next-steps"></a>Следующие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
