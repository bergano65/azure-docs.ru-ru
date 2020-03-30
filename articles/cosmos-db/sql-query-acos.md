---
title: ACOS на языке запросов Azure Cosmos DB
description: Узнайте о том, как система ACOS (arccosice) S'L в Azure Cosmos DB возвращает угол, в радианах, чей косин является указанным числовым выражением
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300967"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Космос DB)
 Возвращает угол в радианах, косинус которого равен указанному числовому выражению; также называется арккосинусом.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример `ACOS` возвращает -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Результирующий набор:  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
