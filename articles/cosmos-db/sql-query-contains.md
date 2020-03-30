---
title: СНАГНОВ На языке запросов Запроса Azure Cosmos DB
description: Узнайте о том, как функция системы CONTAINS S'L в Azure Cosmos DB возвращает Булеану, указывающего, содержит ли первое выражение строки второе
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302599"
---
# <a name="contains-azure-cosmos-db"></a>ССОДЕРЖИТ (Azure Космос DB)
 Возвращает значение логического типа, указывающее, содержит ли первое строковое выражение второе.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr1*  
   Строковое выражение, в котором выполняется поиск.  
  
*str_expr2*  
   Это строка выражение, чтобы найти.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример проверяет, если "abc" содержит "ab" и если "abc" содержит "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Результирующий набор:  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строка функции Azure Космос DB](sql-query-string-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
