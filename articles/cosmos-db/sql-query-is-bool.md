---
title: IS_BOOL на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функции системы S'L IS_BOOL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b7f1cfb09121309e246b314d57a5e4e475bd0983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303874"
---
# <a name="is_bool-azure-cosmos-db"></a>IS_BOOL (Azure Cosmos DB)
 Возвращает логическое значение, указывающее, является ли указанное выражение логическим значением.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
IS_BOOL(<expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*expr*  
   Есть любое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример проверяет объекты JSON Boolean, число, строку, null, `IS_BOOL` объект, массив и неопределенные типы с помощью функции.  
  
```sql
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Результирующий набор:  
  
```json
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  

## <a name="remarks"></a>Remarks

Эта функция системы будет пользоваться [индексом диапазона.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Дальнейшие действия

- [Функции проверки типа Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
