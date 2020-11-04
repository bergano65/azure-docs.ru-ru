---
title: CONCAT на языке запросов Azure Cosmos DB
description: Узнайте, как системная функция CONCAT SQL в Azure Cosmos DB возвращает строку, которая является результатом сцепления двух или более строковых значений.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f5fbbbdd9d29199f5b9bd173b6ab12d3d615943c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339203"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает строку, являющуюся результатом объединения двух или более строковых значений.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение для объединения с другими значениями. `CONCAT`Функции требуется по крайней мере два аргумента *str_expr* .  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
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
  
## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
