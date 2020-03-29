---
title: ПРАВООБЛАДАТЕЛЬ на языке запросов Azure Cosmos DB
description: Узнайте о функции системы СЗЛ RIGHT в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247616d2ac4f2a5799a5896d679f6e6d5917d5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302157"
---
# <a name="right-azure-cosmos-db"></a>ПРАВООБЛАДАТЕЛЬ (Azure Cosmos DB)
 Возвращает правую часть строки с указанным количеством символов.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является ли выражение строки для извлечения символов из.  
  
*num_expr*  
   Это численное выражение, которое определяет количество символов.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
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

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строка функции Azure Космос DB](sql-query-string-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
