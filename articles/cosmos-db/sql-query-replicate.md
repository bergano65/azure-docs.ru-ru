---
title: РЕПЛИКАЦИя на языке запросов Azure Cosmos DB
description: Сведения о репликации системной функции SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aea29cfff6b3827cfb9169722e48120e3a5a3709
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88794326"
---
# <a name="replicate-azure-cosmos-db"></a>РЕПЛИКАЦИя (Azure Cosmos DB)
 Повторяет значение строки указанное число раз.
  
## <a name="syntax"></a>Синтаксис
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение.
  
*num_expr*  
   Числовое выражение. Если *num_expr* является отрицательным или не конечным, результат не определен.
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.
  
## <a name="remarks"></a>Remarks

  Максимальная длина результата — 10 000 символов, например (Length (*str_expr*) *  *num_expr*) <= 10 000. Эта системная функция не будет использовать индекс.

## <a name="examples"></a>Примеры
  
  В следующем примере показано, как использовать `REPLICATE` в запросе.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Результирующий набор:
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
