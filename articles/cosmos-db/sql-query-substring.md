---
title: Подстрока в языке запросов Azure Cosmos DB
description: Сведения о подстроке системной функции SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6cb96270d1db970230e3abb528b58b7341d652f0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081490"
---
# <a name="substring-azure-cosmos-db"></a>Подстрока (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает часть строкового выражения, начиная с указанной позиции (отсчет начинается с нуля) и до достижения указанной длины (или до конца строки).  
  
## <a name="syntax"></a>Синтаксис
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение.
  
*num_expr1*  
   Числовое выражение для обозначения начального символа. Значение 0 является первым символом *str_expr* .
  
*num_expr2*  
   Числовое выражение для обозначения максимального числа символов, возвращаемых *str_expr* . Значение 0 или меньше приводит к пустой строке.

## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  Пример ниже возвращает подстроку "abc", которая начинается с 1 и имеет длину в 1 знак.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Результирующий набор:  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Remarks

Эта системная функция будет использовать [индекс диапазона](index-policy.md#includeexclude-strategy) , если начальная позиция — `0` .

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
