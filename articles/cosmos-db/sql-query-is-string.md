---
title: IS_STRING на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функции системы S'L IS_STRING в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a717f343b0f46522a3ce2bb56c32e3f15998d777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303772"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING (Azure Космос DB)
 Возвращает логическое значение, указывающее, является ли указанное выражение строковым значением.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*expr*  
   Есть любое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример проверяет объекты JSON Boolean, число, строку, null, `IS_STRING` объект, массив и неопределенные типы с помощью функции.  
  
```sql
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Результирующий набор:  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="remarks"></a>Remarks

Эта функция системы будет пользоваться [индексом диапазона.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Дальнейшие действия

- [Функции проверки типа Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
