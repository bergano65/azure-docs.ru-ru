---
title: IS_NUMBER на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функции системы S'L IS_NUMBER в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c019424241bc07d5a5d2cc19a64685c476f94548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303823"
---
# <a name="is_number-azure-cosmos-db"></a>IS_NUMBER (Azure Космос DB)
 Возвращает логическое значение, указывающее, является ли указанное выражение числовым значением.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
IS_NUMBER(<expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*expr*  
   Есть любое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример проверяет объекты JSON Boolean, число, строку, null, `IS_NUMBER` объект, массив и неопределенные типы с помощью функции.  
  
```sql
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 Результирующий набор:  
  
```json
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  

## <a name="remarks"></a>Remarks

Эта функция системы будет пользоваться [индексом диапазона.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Дальнейшие действия

- [Функции проверки типа Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
