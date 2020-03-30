---
title: LOG10 на языке запроса Azure Cosmos DB
description: Узнайте о функции системы LOG10 S'L в Azure Cosmos DB, чтобы вернуть базовый-10 logarithm указанного численного выражения
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302497"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Космос DB)
 Возвращает десятичный логарифм от указанного числового выражения.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expression*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="remarks"></a>Remarks
  
  Функции LOG10 и POWER находятся в обратной зависимости друг от друга. Например, 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Примеры
  
  В примере ниже объявляется переменная и возвращается значение LOG10 указанной переменной (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Результирующий набор:  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
