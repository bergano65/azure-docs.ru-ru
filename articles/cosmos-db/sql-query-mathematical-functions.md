---
title: Математические функции в Azure Cosmos DB языке запросов
description: Сведения о математических функциях в Azure Cosmos DB для выполнения вычислений на основе входных значений, которые предоставляются в качестве аргументов, и возвращают числовое значение.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bd53feb175c5be77f559a4d2e724a55e41df48eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85562823"
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

Следующие поддерживаемые встроенные математические функции выполняют вычисление, обычно на основе входных аргументов и возвращают числовое выражение:
 
* [ПРОСТО](sql-query-abs.md)
* [ACOS](sql-query-acos.md)
* [ASIN](sql-query-asin.md)
* [ATAN](sql-query-atan.md)
* [ATN2](sql-query-atn2.md)
* [ТОЛОК](sql-query-ceiling.md)
* [COS](sql-query-cos.md)
* [COT](sql-query-cot.md)
* [УГОЛ](sql-query-degrees.md)
* [РАСШИРЕНИЕМ](sql-query-exp.md)
* [ФАБРИЧ](sql-query-floor.md)
* [ЖУРНАЛЬ](sql-query-log.md)
* [LOG10](sql-query-log10.md)
* [PI](sql-query-pi.md)
* [POWER](sql-query-power.md)
* [РАДИАНАХ](sql-query-radians.md)
* [Функция](sql-query-rand.md)
* [ОКРУГЛО](sql-query-round.md)
* [ПИСАТЬ](sql-query-sign.md)
* [SIN](sql-query-sin.md)
* [МНИМ](sql-query-sqrt.md)
* [ТЕКАНИЯ](sql-query-square.md)
* [TAN](sql-query-tan.md)
* [TRUNC](sql-query-trunc.md)

  
Все математические функции, кроме RAND, являются детерминированными. Это значит, что они возвращают одни и те же результаты каждый раз, когда вызываются с одними и теми же входными значениями.

## <a name="next-steps"></a>Дальнейшие шаги

- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Определенные пользователем функции](sql-query-udfs.md)
- [Статистические выражения](sql-query-aggregates.md)
