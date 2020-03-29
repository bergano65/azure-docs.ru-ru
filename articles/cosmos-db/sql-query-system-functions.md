---
title: Функция системы на языке запросов Azure Cosmos DB
description: Узнайте о встроенных и пользовательских функциях системы S'L в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870536"
---
# <a name="system-functions-azure-cosmos-db"></a>Функции системы (Azure Cosmos DB)

 Cosmos DB предоставляет множество встроенных функций SQL. Ниже перечислены их категории.  
  
|Группа функций|Описание|Операции|  
|--------------|-----------------|-----------------| 
|[Функции массивов](sql-query-array-functions.md)|Функции массивов выполняют операцию над входным массивом и возвращают числовое, логическое значение либо массив. | [ARRAY_CONCAT,](sql-query-array-concat.md) [ARRAY_CONTAINS,](sql-query-array-contains.md) [ARRAY_LENGTH,](sql-query-array-length.md) [ARRAY_SLICE](sql-query-array-slice.md) |
|[Функции «Дата» и «Время»](sql-query-date-time-functions.md)|Функции даты и времени позволяют получить текущую дату и время UTC в двух формах; числовая отметка времени, значение которой является эпохой Unix в миллисекундах или в виде строки, которая соответствует формату ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Математические функции](sql-query-mathematical-functions.md)|Математические функции выполняют вычисление, которое обычно основано на входных значениях, предоставляемых в форме аргументов, и возвращают числовое значение. | [АБС](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [TAN](sql-query-tan.md) [CEILING](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [DEGREES](sql-query-degrees.md), [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10](sql-query-log10.md) [TRUNC](sql-query-trunc.md) , [PI](sql-query-pi.md), [POWER](sql-query-power.md), [RADIANS](sql-query-radians.md), [RAND](sql-query-rand.md), [ROUND](sql-query-round.md), [SIGN](sql-query-sign.md), [SIN](sql-query-sin.md), [S-S](sql-query-sqrt.md) [SQUARE](sql-query-square.md) |
|[Пространственные функции](sql-query-spatial-functions.md)|Пространственные функции выполняют операцию над входным пространственным объектом и возвращают числовое или логическое значение. | [ST_DISTANCE,](sql-query-st-distance.md) [ST_INTERSECTS,](sql-query-st-intersects.md) [ST_ISVALID,](sql-query-st-isvalid.md) [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md) [ST_WITHIN](sql-query-st-within.md) |
|[Функции строки](sql-query-string-functions.md)|Cтроковые функции выполняют операцию над входным строковым значением и возвращают строковое, числовое или логическое значение. | [КОНКАТ](sql-query-concat.md), [СОНОН](sql-query-contains.md), [КОНСИС](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [LEFT](sql-query-left.md), [LENGTH](sql-query-length.md), [LOWER](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [РЕЛАКС,](sql-query-replace.md) [РЕПРЕТРИЯ](sql-query-replicate.md), [ПРАВО](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md) [REVERSE](sql-query-reverse.md), [StringToObject](sql-query-stringtoobject.md), [SUBSTRING](sql-query-substring.md), [ToString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [UPPER](sql-query-upper.md) |
|[Функции проверки типа](sql-query-type-checking-functions.md)|Функции проверки типа позволяют проверять тип выражения в запросах SQL. | [IS_ARRAY,](sql-query-is-array.md) [IS_BOOL,](sql-query-is-bool.md) [IS_DEFINED,](sql-query-is-defined.md) [IS_NULL,](sql-query-is-null.md) [IS_NUMBER,](sql-query-is-number.md) [IS_OBJECT](sql-query-is-object.md) [IS_STRING,](sql-query-is-string.md) [IS_PRIMITIVE](sql-query-is-primitive.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Встроенные и пользовательские функции (UDF)

Если в настоящее время вы используете функцию, определяемую пользователем (UDF), для которой теперь доступна встроенная функция, соответствующая встроенная функция будет более быстрой и эффективной.

## <a name="built-in-versus-ansi-sql-functions"></a>Встроенные функции по сравнению с функциями ANSI S'L

Основное различие между функциями Cosmos DB и функциями ANSI S'L заключается в том, что функции Cosmos DB хорошо работают с данными о схемах и смешанной схеме. Например, если свойство отсутствует или имеет нечисловое значение, например, `unknown`элемент пропускается вместо возврата ошибки.

## <a name="next-steps"></a>Дальнейшие действия

- [Введение в Azure Космос DB](introduction.md)
- [Функции массивов](sql-query-array-functions.md)
- [Функции даты и времени](sql-query-date-time-functions.md)
- [Математические функции](sql-query-mathematical-functions.md)
- [Пространственные функции](sql-query-spatial-functions.md)
- [Функции строки](sql-query-string-functions.md)
- [Функции проверки типа](sql-query-type-checking-functions.md)
- [Определенные пользователем функции](sql-query-udfs.md)
- [Статистические выражения](sql-query-aggregates.md)
