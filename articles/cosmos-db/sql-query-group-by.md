---
title: Предложение GROUP BY в Azure Cosmos DB
description: Сведения о предложении GROUP BY в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: 48f272ea9005d8eb24f639b4039a84163441cffa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101448"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Предложение GROUP BY в Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Предложение GROUP BY разделяет результаты запроса по значениям одного или нескольких указанных свойств.

## <a name="syntax"></a>Синтаксис

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Аргументы

- `<scalar_expression_list>`

   Указывает выражения, которые будут использоваться для разделения результатов запроса.

- `<scalar_expression>`
  
   Допускается любое скалярное выражение за исключением вложенных запросов и статистических выражений. Каждое скалярное выражение должно содержать ссылку хотя бы на один столбец. Количество отдельных выражений или количество элементов в каждом выражении не ограничиваются.

## <a name="remarks"></a>Remarks
  
  Если в запросе используется предложение GROUP BY, предложение SELECT может содержать только подмножество свойств и системных функций, входящих в предложение GROUP BY. Единственным исключением являются [агрегатные системные функции](sql-query-aggregates.md), которые могут присутствовать в предложении SELECT без включения в предложение GROUP BY. Также в предложение SELECT всегда можно включать литеральные значения.

  Предложение GROUP BY должно располагаться после предложений SELECT, FROM и WHERE, но перед предложением OFFSET LIMIT. В настоящее время нельзя использовать GROUP BY с предложением ORDER BY, но запланирована поддержка такого варианта.

  В предложении GROUP BY не допускаются следующие механизмы.
  
- Присвоение псевдонимов свойствам и (или) системным функциям (но это обычным образом возможно в предложении SELECT).
- Вложенные запросы
- Агрегатные системные функции (они разрешены только в предложении SELECT).

Не поддерживаются запросы с агрегатной системной функцией и вложенным запросом с `GROUP BY`. Например, следующий запрос выполнить не удастся:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>Примеры

В этих примерах используется набор данных о питательной ценности, доступный на [площадке для тестирования запросов Azure Cosmos DB](https://www.documentdb.com/sql/demo).

Например, такой запрос возвращает общее количество элементов в каждой группе foodGroup:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Вот пример возвращаемых им результатов (для ограничения выходных данных используется ключевое слово TOP):

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

Этот запрос содержит два выражения для деления результатов:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Вот пример его результатов:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

Этот запрос содержит системную функцию в предложении GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Вот пример его результатов:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

В этом запросе выражение свойства элемента содержит и ключевые слова, и системные функции:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Результаты:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы](sql-query-getting-started.md)
- [Предложение SELECT](sql-query-select.md)
- [Агрегатные функции](sql-query-aggregates.md)
