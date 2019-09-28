---
title: Подстрока в языке запросов Azure Cosmos DB
description: Сведения о подстроке системной функции SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bf14bda9bd1acc62820bf07f83ac074a8d1b691c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349193"
---
# <a name="substring-azure-cosmos-db"></a>Подстрока (Azure Cosmos DB)
 Возвращает часть строкового выражения, начиная с указанной позиции (отсчет начинается с нуля) и до достижения указанной длины (или до конца строки).  
  
## <a name="syntax"></a>Синтаксис
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является строковым выражением.
  
*num_expr1*  
   Числовое выражение для обозначения начального символа. Значение 0 является первым символом *str_expr*.
  
*num_expr2*  
   Числовое выражение для обозначения максимального числа возвращаемых символов *str_expr* . Значение 0 или меньше приводит к пустой строке.

## <a name="return-types"></a>Возвращаемые типы
  
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

## <a name="next-steps"></a>Следующие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
