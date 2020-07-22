---
title: StringEquals на языке запросов Azure Cosmos DB
description: Сведения о том, как системная функция SQL StringEquals в Azure Cosmos DB возвращает значение логического типа, указывающее, совпадает ли первое строковое выражение со вторым
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 57d124421082e1c38fab4d982687a8e6c970505e
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853696"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)

 Возвращает значение логического типа, указывающее, совпадает ли первое строковое выражение со вторым.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr1*  
   Первое строковое выражение для сравнения.  
  
*str_expr2*  
   Второе строковое выражение для сравнения.  

*bool_expr* Необязательное значение для игнорирования регистра. Если задано значение true, StringEquals будет выполнять поиск без учета регистра. Если значение не указано, оно равно false.
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере проверяется совпадение "abc" с "abc" и "abc" с "ABC".  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
```  
  
 Результирующий набор:  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Remarks

Эта системная функция воспользуется преимуществами [индекса диапазона](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
