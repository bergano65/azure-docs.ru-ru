---
title: Стрингтобулеан на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Стрингтобулеан в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8719857dca16585a045f8174dbac8df455f38f38
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349257"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>Стрингтобулеан (Azure Cosmos DB)
 Возвращает выражение, преобразованное в логическое значение. Если выражение не может быть преобразовано, возвращает значение undefine.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение, анализируемое как логическое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает логическое выражение или значение undefine.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как `StringToBoolean` ведет себя по разным типам. 
 
 Ниже приведены примеры с допустимыми входными данными.

Пробелы допускаются только до или после "true"/"false".

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

Ниже приведены примеры с недопустимыми входными данными.

 Логические значения вводятся с учетом регистра и должны быть записаны со всеми символами нижнего регистра, например "true" и "false".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Результирующий набор:  
  
```json
[{}]
``` 

Переданное выражение будет проанализировано как логическое выражение. для этих входных данных не вычисляется тип Boolean, поэтому возвращается значение undefine.

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

## <a name="next-steps"></a>Следующие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
