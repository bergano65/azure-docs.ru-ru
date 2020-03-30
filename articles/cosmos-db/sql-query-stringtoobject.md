---
title: StringToObject на языке запросов Azure Cosmos DB
description: Узнайте о функции системы Стринг-Товобъект в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296387"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Возвращает выражение, переведенное на объект. Если выражение не может быть переведено, возвращается неопределенным.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является выражением строки, чтобы быть разогнанным как выражение объекта JSON. Обратите внимание, что вложенные значения строки должны быть написаны с двойными кавычками, чтобы быть действительными. Подробнее о формате JSON смотрите [json.org](https://json.org/)  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает выражение объекта или неопределенный.  
  
## <a name="examples"></a>Примеры
  
  Ниже приводится `StringToObject` следующий пример, как ведет себя в разных типах. 
  
 Ниже приведены примеры с допустимыми входными мерами.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Результирующий набор:

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Ниже приведены примеры с недействительными входными.
Даже если они действительны в запросе, они не будут разбирать допустимые объекты. Строки внутри строки объекта должны либо\\быть\\избежаны "a" ::\\"str\\"", либо окружающая цитата должна быть одной 'a': "str"".

Отдельные кавычки, окружающие имена свойств, недействительны JSON.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Результирующий набор:

```json
[{}]
```  

Имена свойств без окружающих котировок не действительны JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Результирующий набор:

```json
[{}]
``` 

Ниже приведены примеры с недействительными входными.

 Пройденое выражение будет разогнано как объект JSON; эти входы не оцениваются для ввода объекта и, таким образом, возвращаются неопределенными.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
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
