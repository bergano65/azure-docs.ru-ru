---
title: Язык запросов Azure Cosmos DB ЭТАЖЕй
description: Сведения о системной функции FLOOR SQL в Azure Cosmos DB для возврата самого длинного целого числа, меньшего или равного указанному числовому выражению
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2a1862355495986e9910c48cf3b8e1337e4c7fb0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100242"
---
# <a name="floor-azure-cosmos-db"></a>ЭТАЖ (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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

Эта системная функция воспользуется преимуществами [индекса диапазона](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
