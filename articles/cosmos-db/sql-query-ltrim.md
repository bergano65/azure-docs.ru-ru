---
title: LTRIM в языке запросов Azure Cosmos DB
description: Сведения о системной функции LTRIM SQL в Azure Cosmos DB для возврата строкового выражения после удаления начальных пробелов
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 08c069de70684a8562e86963ddb2e84ee889e7cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302259"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (Azure Cosmos DB)
 Возвращает строковое выражение после удаления начальных пробелов.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является строковым выражением.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как использовать `LTRIM` внутри запроса.  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Результирующий набор:  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство с Azure Cosmos DB](introduction.md)
