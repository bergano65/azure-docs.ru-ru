---
title: СОДЕРЖИТ в Azure Cosmos DB языке запросов
description: Сведения о том, как функция CONTAINS SQL System в Azure Cosmos DB возвращает логическое значение, указывающее, содержит ли первое строковое выражение вторую
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302599"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
 Возвращает значение логического типа, указывающее, содержит ли первое строковое выражение второе.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr1*  
   Строковое выражение, в котором выполняется поиск.  
  
*str_expr2*  
   Искомое строковое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере проверяется, содержит ли "ABC" "AB", а если "ABC" содержит "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Результирующий набор:  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство с Azure Cosmos DB](introduction.md)
