---
title: ATN2 на языке запросов Azure Cosmos DB
description: Узнайте о том, как системная функция ATN2 SQL в Azure Cosmos DB возвращает основное значение тангенса дуги y/x, выраженное в радианах.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 696e14e75998ead04c99fab2b84fc4c742d5f54a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302667"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Возвращает основное значение арктангенса y/x, выраженное в радианах.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В примере ниже вычисляется ATN2 для указанных компонентов x и y.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Результирующий набор:  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
