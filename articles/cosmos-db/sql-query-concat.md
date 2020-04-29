---
title: CONCAT на языке запросов Azure Cosmos DB
description: Узнайте, как системная функция CONCAT SQL в Azure Cosmos DB возвращает строку, которая является результатом сцепления двух или более строковых значений.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302616"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Возвращает строку, являющуюся результатом объединения двух или более строковых значений.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение для объединения с другими значениями. `CONCAT` Функции требуется по крайней мере два аргумента *str_expr* .  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  Пример ниже возвращает объединенную строку указанных значений.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Результирующий набор:  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство с Azure Cosmos DB](introduction.md)
