---
title: Стрингтонулл на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Стрингтонулл в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c7ea3ca82cadb1351d8581f3d652ce060d67969b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349249"
---
# <a name="stringtonull-azure-cosmos-db"></a>Стрингтонулл (Azure Cosmos DB)
 Возвращает выражение, преобразованное в значение null. Если выражение не может быть преобразовано, возвращает значение undefine.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение, анализируемое как выражение null.
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает пустое или неопределенное выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как `StringToNull` ведет себя по разным типам. 

Ниже приведены примеры с допустимыми входными данными.

 Пробелы допускаются только до или после "null".

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

Ниже приведены примеры с недопустимыми входными данными.

NULL учитывается с учетом регистра и должен быть записан со всеми символами нижнего регистра, например "null".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Результирующий набор:  
  
```json
[{}]
```  

Переданное выражение будет проанализировано как выражение null; Эти входные данные не вычисляют значение NULL и, таким же, возвращают неопределенный тип.

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

## <a name="next-steps"></a>Следующие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
