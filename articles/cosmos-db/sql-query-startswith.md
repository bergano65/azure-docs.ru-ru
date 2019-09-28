---
title: STARTSWITH на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System STARTSWITH в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a7822425f17d6e121dfcb20d8766f0b3bc7032a2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349314"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 Возвращает значение логического типа, указывающее, начинается ли первое строковое выражение вторым.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr1*  
   Является строковым выражением.
  
*str_expr2*  
   Строковое выражение, сравниваемое с началом *str_expr1*.

## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  Пример ниже проверяет, начинается ли строка "abc" с "b" и "a".  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Результирующий набор:  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="next-steps"></a>Следующие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
