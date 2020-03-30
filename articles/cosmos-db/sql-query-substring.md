---
title: SUBSTRING на языке запросов Azure Cosmos DB
description: Узнайте о функции системы SUBSTRING в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303704"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
 Возвращает часть строкового выражения, начиная с указанной позиции (отсчет начинается с нуля) и до достижения указанной длины (или до конца строки).  
  
## <a name="syntax"></a>Синтаксис
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является выражением строки.
  
*num_expr1*  
   Является числовым выражением для обозначения начального персонажа. Значение 0 является первым символом *str_expr*.
  
*num_expr2*  
   Является числовым выражением, обозначить максимальное количество символов *str_expr,* которые будут возвращены. Значение 0 или менее приводит к пустой строке.

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

Эта функция системы будет пользоваться [индексом диапазона,](index-policy.md#includeexclude-strategy) если исходная `0`позиция.

## <a name="next-steps"></a>Дальнейшие действия

- [Строка функции Azure Космос DB](sql-query-string-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
