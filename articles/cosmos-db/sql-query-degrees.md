---
title: DEGREES на языке запросов Azure Cosmos DB
description: Узнайте о функции системы DEGREES S'L в Azure Cosmos DB, чтобы вернуть соответствующий угол в градусах для угла, указанного в радиане
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299476"
---
# <a name="degrees-azure-cosmos-db"></a>DEGREES (Azure Cosmos DB)
 Возвращает соответствующее значение угла в градусах для угла, указанного в радианах.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример возвращает число градусов в угле, равном PI/2 радиан.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Результирующий набор:  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
