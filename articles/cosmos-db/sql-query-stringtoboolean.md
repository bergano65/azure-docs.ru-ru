---
title: StringToBoolean на языке запросов Azure Cosmos DB
description: Узнайте о функции системы S'L StringToBoolean в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296547"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Космос DB)
 Возвращает выражение, переведенное на boolean. Если выражение не может быть переведено, возвращается неопределенным.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Это строка выражение следует разогнать как выражение Boolean.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает выражение Булеан или неопределенный.  
  
## <a name="examples"></a>Примеры
  
  Ниже приводится `StringToBoolean` следующий пример, как ведет себя в разных типах. 
 
 Ниже приведены примеры с допустимыми входными мерами.

Whitespace допускается только до или после "истинного"/"ложного".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Результирующий набор:  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Ниже приведены примеры с недействительными входными.

 Booleans являются случае чувствительны и должны быть написаны со всеми символами нижнего регистра, т.е. "истинный" и "ложный".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Результирующий набор:  
  
```json
[{}]
``` 

Пройденое выражение будет разогнано как выражение Булеан; эти входы не оцениваются для ввода Boolean и, таким образом, возвращаются неопределенными.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Результирующий набор:  
  
```json
[{}]
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строка функции Azure Космос DB](sql-query-string-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
