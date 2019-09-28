---
title: ЗАМЕНИТЬ на языке запросов Azure Cosmos DB
description: Сведения о функции "замена системных функций SQL" в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2cb7d82efd010fd7c3395a4f6a9217370d9e5779
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349581"
---
# <a name="replace-azure-cosmos-db"></a>Replace (Azure Cosmos DB)
 Заменяет все вхождения указанного строкового значения другим строковым значением.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr1*  
   Строковое выражение для поиска.  
  
*str_expr2*  
   Строковое выражение, которое должно быть найдено.  
  
*str_expr3*  
   Строковое выражение для замены вхождений *str_expr2* в *str_expr1*.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как использовать `REPLACE` в запросе.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Результирующий набор:  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="next-steps"></a>Следующие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
