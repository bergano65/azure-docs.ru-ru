---
title: StringToArray на языке запросов Azure Cosmos DB
description: Узнайте о функции системы S'L StringToArray в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302922"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Возвращает выражение, переведенное в массив. Если выражение не может быть переведено, возвращается неопределенным.  
  
## <a name="syntax"></a>Синтаксис
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является выражением строки, чтобы быть разогнанным как выражение JSON Array. 
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает выражение или неопределенный массив. 
  
## <a name="remarks"></a>Remarks
  Значения nested строки должны быть написаны с двойными кавычками, чтобы быть действительными JSON. Подробнее о формате JSON смотрите [json.org](https://json.org/)
  
## <a name="examples"></a>Примеры
  
  Ниже приводится `StringToArray` следующий пример, как ведет себя в разных типах. 
  
 Ниже приведены примеры с допустимыми входными мерами.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Результирующий набор:

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Ниже приводится пример недействительного ввода. 
   
 Отдельные котировки в массиве недействительны JSON.
Даже если они действительны в запросе, они не будут разбираться с допустимыми массивами. Строки внутри строки массива должны\\\\быть либо избежаны , или окружающая цитата должна быть одной """"."

```sql
SELECT
    StringToArray("['5','6','7']")
```

Результирующий набор:

```json
[{}]
```

Ниже приведены примеры недействительных входных мер.
   
 Пройденое выражение будет разогнано как массив JSON; следующие не оценивают сятворный для того чтобы напечатать массив и таким образом возвратить неопределенный.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
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
