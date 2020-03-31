---
title: LOWER на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функции системы LOWER S'L в Azure Cosmos DB, чтобы вернуть выражение строки после преобразования данных символов верхнего регистра в нижний регистр
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302276"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 Возвращает строковое выражение после преобразования символов верхнего регистра в нижний.  

Функция системы LOWER не использует индекс. Если вы планируете делать частые нечувствительные сравнения, функция системы LOWER может потреблять значительное количество RU. Если это так, вместо того, чтобы использовать функцию системы LOWER для нормализации данных каждый раз для сравнения, можно нормализовать оболочку при вставке. Затем такой запрос, как SELECT , от c ГДЕ LOWER (c.name) - "боб" просто становится SELECT с где c.name "боб".

## <a name="syntax"></a>Синтаксис
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является выражением строки.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере `LOWER` показано, как использовать в запросе.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Результирующий набор:  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строка функции Azure Космос DB](sql-query-string-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
