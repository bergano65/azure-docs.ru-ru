---
title: ARRAY_CONCAT языка запросов Azure Cosmos DB
description: Узнайте, как системная функция Concat массива SQL в Azure Cosmos DB возвращает массив, являющийся результатом сцепления двух или более значений массива.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 10370e16c95f4fc747dd3a66a56794da38562972
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871828"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Возвращает массив, который является результатом объединения значений двух или более массивов.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Аргументы
  
*arr_expr*  
   Выражение массива для сцепления с другими значениями. Для функции `ARRAY_CONCAT` требуется по крайней мере два аргумента *arr_expr* .  
  
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
  

## <a name="next-steps"></a>Дальнейшие действия

- [Функции массива Azure Cosmos DB](sql-query-array-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
