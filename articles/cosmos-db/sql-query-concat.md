---
title: CONCAT на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System CONCAT в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e61d61a3d64ca7d7808619159e4dfc8e8b33d68
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351264"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Возвращает строку, являющуюся результатом объединения двух или более строковых значений.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение для объединения с другими значениями. Функции `CONCAT` требуются по крайней мере два аргумента *str_expr* .  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  Пример ниже возвращает объединенную строку указанных значений.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Результирующий набор:  
  
```json
[{"concat": "abcdef"}]  
```  
  

## <a name="next-steps"></a>Следующие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
