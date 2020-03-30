---
title: StringToNumber на языке запросов Azure Cosmos DB
description: Узнайте о функции системы S'L StringToNumber в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296428"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Возвращает выражение, переведенное на число. Если выражение не может быть переведено, возвращается неопределенным.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является выражением строки, которая должна быть разогнана как выражение номера JSON. Номера в JSON должны быть бесчисленно или плавающей точкой. Подробнее о формате JSON смотрите [json.org](https://json.org/)  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает выражение номера или неопределенный.  
  
## <a name="examples"></a>Примеры
  
  Ниже приводится `StringToNumber` следующий пример, как ведет себя в разных типах. 

Белое пространство разрешено только до или после номера.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Результирующий набор:  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

В JSON действительный номер должен быть либо целым числом, либо номером плавающей точки.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Результирующий набор:  
  
```json
{{}}
```  

Пройденое выражение будет разогнано как выражение Числа; эти входы не оцениваются для ввода числа и, таким образом, возвращаются неопределенными. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Результирующий набор:  
  
```json
{{}}
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строка функции Azure Космос DB](sql-query-string-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
