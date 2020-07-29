---
title: LEFT на языке запросов Azure Cosmos DB
description: Сведения о функции системы SQL, ОСТАВЛЕНной в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303755"
---
# <a name="left-azure-cosmos-db"></a>LEFT (Azure Cosmos DB)
 Возвращает левую часть строки с указанным количеством символов.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение, из которого извлекаются символы.  
  
*num_expr*  
   Числовое выражение, которое указывает количество символов.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  Пример ниже возвращает левую часть строки "abc" для значений различной длины.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Результирующий набор:  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция воспользуется преимуществами [индекса диапазона](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
