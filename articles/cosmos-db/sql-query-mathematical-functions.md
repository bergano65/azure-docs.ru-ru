---
title: Математические функции в Azure Cosmos DB языке запросов
description: Сведения о математических системных функциях SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fe4a84db3f2cbcfc2d9841caf520404afec2a297
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349669"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Математические функции (Azure Cosmos DB)  

Математические функции выполняют вычисление, которое основано на входных значениях, предоставляемых в форме аргументов, и возвращают числовое значение.

Вы можете выполнять запросы, как в следующем примере:

```sql
    SELECT VALUE ABS(-4)
```

Результат:

```json
    [4]
```

## <a name="functions"></a>Функции

Следующие поддерживаемые встроенные математические функции выполняют вычисление, обычно на основе входных аргументов и возвращают числовое выражение.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[FLOOR](sql-query-floor.md)|[LOG](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[ROUND](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[SIN](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Все математические функции, за исключением RAND, являются детерминированными функциями. Это означает, что они возвращают одинаковые результаты при каждом вызове с помощью определенного набора входных значений.

## <a name="next-steps"></a>Следующие шаги

- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Определяемые пользователем функции](sql-query-udfs.md)
- [Статистические выражения](sql-query-aggregates.md)
