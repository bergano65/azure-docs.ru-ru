---
title: IS_DEFINED на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функциях системы S'L IS_DEFINED в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303857"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Возвращает логическое значение, указывающее, назначено ли свойству значение.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*expr*  
   Есть любое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  В примере ниже проверяется наличие свойства в указанном документе JSON. Первый пример возвращает значение true, так как присутствует значение "a", но второй возвращает значение false, так как значение "b" отсутствует.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Результирующий набор:  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>Remarks

Эта функция системы будет пользоваться [индексом диапазона.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Дальнейшие действия

- [Функции проверки типа Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
