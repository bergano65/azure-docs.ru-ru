---
title: UPPER на языке запросов Azure Cosmos DB
description: Узнайте о функции системы СЗЛ UPPER в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303976"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Возвращает строковое выражение после преобразования символов нижнего регистра в верхний.  

Функция системы UPPER не использует индекс. Если вы планируете делать частые нечувствительные сравнения, функция системы UPPER может потреблять значительное количество RU. Если это так, вместо того, чтобы использовать функцию системы UPPER для нормализации данных каждый раз для сравнения, можно нормализовать оболочку при вставке. Затем такой запрос, как SELECT , от c ГДЕ UPPER (c.name) - 'BOB' просто становится SELECT - от c ГДЕ c.name "BOB".

## <a name="syntax"></a>Синтаксис
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является выражением строки.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример показывает, `UPPER` как использовать в запросе  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Результирующий набор:  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строка функции Azure Космос DB](sql-query-string-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
