---
title: Математические функции на языке запросов Azure Cosmos DB
description: Узнайте о математических функциях в Azure Cosmos DB для выполнения вычислений, основанных на значениях ввода, которые приведены в качестве аргументов, и верните числовое значение.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873273"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Математические функции (Azure Cosmos DB)  

Математические функции выполняют вычисление, которое основано на входных значениях, предоставляемых в форме аргументов, и возвращают числовое значение.

Вы можете запускать запросы, например следующий пример:

```sql
    SELECT VALUE ABS(-4)
```

Результат:

```json
    [4]
```

## <a name="functions"></a>Функции

Следующие поддерживаемые встроенные математические функции выполняют вычисление, обычно на основе входных аргументов, и возвращают числовое выражение.
  
||||  
|-|-|-|  
|[Abs](sql-query-abs.md)|[АКОС](sql-query-acos.md)|[Asin](sql-query-asin.md)|  
|[Atan](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[Градусов](sql-query-degrees.md)|  
|[Exp](sql-query-exp.md)|[Этаже](sql-query-floor.md)|[Журнала](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[Мощность](sql-query-power.md)|  
|[Радианах](sql-query-radians.md)|[RAND](sql-query-rand.md)|[Круглый](sql-query-round.md)|
|[Знак](sql-query-sign.md)|[Грех](sql-query-sin.md)|[Sqrt](sql-query-sqrt.md)|
|[Площади](sql-query-square.md)|[Загар](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Все математические функции, кроме RAND, являются детерминированными. Это значит, что они возвращают одни и те же результаты каждый раз, когда вызываются с одними и теми же входными значениями.

## <a name="next-steps"></a>Дальнейшие действия

- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
- [Определенные пользователем функции](sql-query-udfs.md)
- [Статистические выражения](sql-query-aggregates.md)
