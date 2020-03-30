---
title: IS_OBJECT на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функции системы S'L IS_OBJECT в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43d8237d859249317d7b581e25ed9a2b34d39d00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303806"
---
# <a name="is_object-azure-cosmos-db"></a>IS_OBJECT (Azure Космос DB)
 Возвращает логическое значение, указывающее, является ли указанное выражение объектом JSON.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
IS_OBJECT(<expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*expr*  
   Есть любое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример проверяет объекты JSON Boolean, число, строку, null, `IS_OBJECT` объект, массив и неопределенные типы с помощью функции.  
  
```sql
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Результирующий набор:  
  
```json
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  

## <a name="remarks"></a>Remarks

Эта функция системы будет пользоваться [индексом диапазона.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Дальнейшие действия

- [Функции проверки типа Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
