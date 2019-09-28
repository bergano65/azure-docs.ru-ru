---
title: ARRAY_CONCAT на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System ARRAY_CONCAT в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ad973650ac205313f9045c170f99e15e385a82d1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348715"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Возвращает массив, который является результатом объединения значений двух или более массивов.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Аргументы
  
*arr_expr*  
   Выражение массива для сцепления с другими значениями. Функции `ARRAY_CONCAT` требуются по крайней мере два аргумента *arr_expr* .  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает выражение массива.  
  
## <a name="examples"></a>Примеры
  
  В примере ниже показано, как объединить два массива.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Результирующий набор:  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  

## <a name="next-steps"></a>Следующие шаги

- [Функции массива Azure Cosmos DB](sql-query-array-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
