---
title: Верхний Azure Cosmos DB язык запросов
description: Сведения о функции SQL System в верхней части Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303976"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Возвращает строковое выражение после преобразования символов нижнего регистра в верхний.  

Верхняя системная функция не использует индекс. Если планируется регулярное сравнение без учета регистра, то верхняя системная функция может потреблять значительное количество единиц запросов. В этом случае вместо использования верхней системной функции для нормализации данных каждый раз при сравнении можно нормализовать регистр при вставке. Затем запрос, например SELECT * FROM c, где UPPER (c. Name) = "BOB", просто превращается в SELECT * FROM c, где c.name = ' BOB '.

## <a name="syntax"></a>Синтаксис
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является строковым выражением.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как использовать `UPPER` в запросе.  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Результирующий набор:  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
