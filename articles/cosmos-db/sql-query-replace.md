---
title: REPLACE на языке запросов Azure Cosmos DB
description: Узнайте о функции системы СЗЛ REPLACE в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302208"
---
# <a name="replace-azure-cosmos-db"></a>РЕПЛАЦИЯ (Azure Космос DB)
 Заменяет все вхождения указанного строкового значения другим строковым значением.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr1*  
   Строковое выражение, в котором выполняется поиск.  
  
*str_expr2*  
   Найдено ли выражение строки.  
  
*str_expr3*  
   Является ли выражение строки для замены случаев *str_expr2* в *str_expr1.*  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере `REPLACE` показано, как использовать в запросе.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Результирующий набор:  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строка функции Azure Космос DB](sql-query-string-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
