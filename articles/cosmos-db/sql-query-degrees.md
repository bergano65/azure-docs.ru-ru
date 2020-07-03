---
title: ГРАДУСы на языке запросов Azure Cosmos DB
description: Сведения о функции SQL System в ГРАДУСАХ в Azure Cosmos DB, чтобы получить соответствующий угол в градусах для угла, указанного в радианах
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78299476"
---
# <a name="degrees-azure-cosmos-db"></a>ГРАДУСы (Azure Cosmos DB)
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

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство с Azure Cosmos DB](introduction.md)
