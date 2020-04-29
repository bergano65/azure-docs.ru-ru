---
title: ARRAY_LENGTH языка запросов Azure Cosmos DB
description: Сведения о том, как системная функция SQL Length массива в Azure Cosmos DB возвращает число элементов указанного выражения массива
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3596ce4bc702d5e54225d8c90db2f9563feab670
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303993"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 Возвращает число элементов массива, указанного в выражении.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*arr_expr*  
   Выражение массива.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как получить длину массива с помощью `ARRAY_LENGTH`.  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Результирующий набор:  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Функции массива Azure Cosmos DB](sql-query-array-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство с Azure Cosmos DB](introduction.md)
