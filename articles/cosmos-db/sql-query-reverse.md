---
title: REVERSE на языке запросов Azure Cosmos DB
description: Узнайте о функции системы СЗЛ REVERSE в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22e1c8a5f4350bd2f966ee48f96368c648a4a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302174"
---
# <a name="reverse-azure-cosmos-db"></a>REVERSE (Azure Cosmos DB)
 Возвращает строковое значение, где символы переставлены в обратном порядке справа налево.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является выражением строки.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере `REVERSE` показано, как использовать в запросе.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Результирующий набор:  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строка функции Azure Космос DB](sql-query-string-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
