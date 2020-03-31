---
title: COS на языке запросов Azure Cosmos DB
description: Узнайте о том, как система Cosine (COS) S'L в Azure Cosmos DB возвращает тригонометрический косин указанного угла, в радиане, в указанном выражении
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304027"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Возвращает тригонометрический косинус указанного угла в радианах в указанном выражении.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример вычисляет `COS` указанный угол.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Результирующий набор:  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
