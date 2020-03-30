---
title: ATAN на языке запроса Azure Cosmos DB
description: Узнайте о том, как система Arctangent (ATAN) S'L в Azure Cosmos DB возвращает угол, в радиане, касательной которых является указанное числовое выражение
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302684"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Космос DB)
 Возвращает угол в радианах, тангенс которого равен указанному числовому выражению. Эта функция арктангенсом.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример `ATAN` возвращает указанное значение.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Результирующий набор:  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
