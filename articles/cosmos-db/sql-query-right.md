---
title: СПРАВА на языке Azure Cosmos DB запросов
description: Сведения о функции SQL System, прямо в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5aeee91db0b1ce891d7e4090a074ddda1f15a576
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349536"
---
# <a name="right-azure-cosmos-db"></a>RIGHT (Azure Cosmos DB)
 Возвращает правую часть строки с указанным количеством символов.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение, из которого извлекаются символы.  
  
*num_expr*  
   Числовое выражение, которое указывает количество символов.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  Пример ниже возвращает правую часть строки "abc" для значений различной длины.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Результирующий набор:  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="next-steps"></a>Следующие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
