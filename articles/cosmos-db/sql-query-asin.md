---
title: ASIN в языке запросов Azure Cosmos DB
description: Узнайте о том, как система Arcsine (ASIN) S'L в Azure Cosmos DB возвращает угол, в радианах, чья синус является указанным числовым выражением
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302701"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Космос DB)
 Возвращает угол в радианах, синус которого равен указанному числовому выражению. Также называется арксинусом.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример `ASIN` возвращает -1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Результирующий набор:  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
