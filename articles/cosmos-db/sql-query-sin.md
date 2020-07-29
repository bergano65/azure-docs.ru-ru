---
title: SIN на языке запросов Azure Cosmos DB
description: Сведения о системной функции SQL SIN в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25e7cf66fdd55a0b641c35443e38b0a67cbe365d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303109"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 Возвращает тригонометрический синус заданного угла в радианах для указанного выражения.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере вычисляется `SIN` указанный угол.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Результирующий набор:  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
