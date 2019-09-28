---
title: IS_OBJECT на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System IS_OBJECT в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8e55cda57202ce5a08257f1448adc828764ac817
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349830"
---
# <a name="is_object-azure-cosmos-db"></a>IS_OBJECT (Azure Cosmos DB)
 Возвращает логическое значение, указывающее, является ли указанное выражение объектом JSON.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
IS_OBJECT(<expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*expr*  
   Любое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере выполняется проверка объектов логического типа, числа, строки, null, объекта, массива и неопределенных типов с помощью функции `IS_OBJECT`.  
  
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

## <a name="next-steps"></a>Следующие шаги

- [Функции проверки типов Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
