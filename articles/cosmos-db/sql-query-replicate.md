---
title: РЕПЛИКАЦИя на языке запросов Azure Cosmos DB
description: Сведения о репликации системной функции SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302191"
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
  
## <a name="remarks"></a>Комментарии
  Максимальная длина результата — 10 000 символов, например (Length (*str_expr*) * *num_expr*) <= 10 000.

## <a name="examples"></a>Примеры
  
  В следующем примере показано, как использовать `REPLICATE` в запросе.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Результирующий набор:
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
