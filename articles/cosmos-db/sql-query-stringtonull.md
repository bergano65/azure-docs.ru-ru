---
title: StringToNull на языке запросов Azure Cosmos DB
description: Узнайте о функции системы S'L StringToNull в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296445"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Возвращает выражение, переведенное на нулевую. Если выражение не может быть переведено, возвращается неопределенным.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является ли выражение строки, чтобы быть разогнаны как недействительное выражение.
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает нулевое выражение или неопределенный.  
  
## <a name="examples"></a>Примеры
  
  Ниже приводится `StringToNull` следующий пример, как ведет себя в разных типах. 

Ниже приведены примеры с допустимыми входными мерами.

 Whitespace допускается только до или после "null".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Результирующий набор:  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Ниже приведены примеры с недействительными входными.

Null является чувствительным к делу и должен быть написан со всеми символами нижнего регистра, т.е. "null".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Результирующий набор:  
  
```json
[{}]
```  

Пройденое выражение будет разогнано как нулевое выражение; эти входные данные не оцениваются для ввода нулевых и, таким образом, возврата неопределенными.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
