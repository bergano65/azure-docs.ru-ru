---
title: Язык запросов ROUND Azure Cosmos DB
description: Дополнительные сведения о функции SQL System ROUND в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 41b1372cd5165b3548a4e574e7eb037111188bac
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341558"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
## <a name="remarks"></a>Комментарии
  
  Операция округления выполняется после среднего значения, округленного от нуля. Если входное значение является числовым выражением, которое находится в точности между двумя целыми числами, результатом будет ближайшее целое число от нуля. Эта системная функция будет использовать преимущества [индекса диапазона](index-policy.md#includeexclude-strategy).
  
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

## <a name="next-steps"></a>Дальнейшие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
