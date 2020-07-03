---
title: Язык запросов Azure Cosmos DB ЭТАЖЕй
description: Сведения о системной функции FLOOR SQL в Azure Cosmos DB для возврата самого длинного целого числа, меньшего или равного указанному числовому выражению
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04dfa6a028cf7c44bf99c665b396d51d8a0f3cef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303194"
---
# <a name="floor-azure-cosmos-db"></a>ЭТАЖ (Azure Cosmos DB)
 Возвращает наибольшее целое число, меньшее или равное указанному числовому выражению.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показаны положительные числовые, отрицательные и нулевые значения с `FLOOR` функцией.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Результирующий набор:  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>Remarks

Эта системная функция будет полезна из [индекса диапазона](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство с Azure Cosmos DB](introduction.md)
