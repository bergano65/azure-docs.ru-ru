---
title: РЕПЛИКАЦИя на языке запросов Azure Cosmos DB
description: Сведения о репликации системной функции SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5e0b7f29c503daa8a95dcc46238e60728c0cec50
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349569"
---
# <a name="replicate-azure-cosmos-db"></a>РЕПЛИКАЦИя (Azure Cosmos DB)
 Повторяет строковое значение указанное число раз.
  
## <a name="syntax"></a>Синтаксис
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является строковым выражением.
  
*num_expr*  
   Числовое выражение. Если *num_expr* является отрицательным или не конечным, результат не определен.
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает строковое выражение.
  
## <a name="remarks"></a>Примечания
  Максимальная длина результата — 10 000 символов, например (Length (*str_expr*) * *num_expr*) < = 10 000.

## <a name="examples"></a>Примеры
  
  В следующем примере показано, как использовать `REPLICATE` в запросе.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Результирующий набор:
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Следующие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
