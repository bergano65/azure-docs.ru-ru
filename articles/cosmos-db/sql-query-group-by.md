---
title: Положение GROUP BY в Azure Cosmos DB
description: Узнайте о пункте GROUP BY для Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: e41e81457421bfe27e3c0313fc06e39e6df4cdce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819104"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Положение GROUP BY в Azure Cosmos DB

Положение GROUP BY делит результаты запроса в зависимости от значений одного или нескольких указанных свойств.

> [!NOTE]
> Azure Cosmos DB в настоящее время поддерживает GROUP BY в .NET SDK 3.3 и выше, а также JavaScript SDK 3.4 и выше.
> Поддержка других языков SDK в настоящее время не доступна, но планируется.

## <a name="syntax"></a>Синтаксис

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Аргументы

- `<scalar_expression_list>`

   Определяет выражения, которые будут использоваться для разделения результатов запроса.

- `<scalar_expression>`
  
   Допускается любое скалярное выражение, за исключением scalar subqueries и scalar агрегатов. Каждое скалярное выражение должно содержать по крайней мере одну ссылку на свойствы. Количество отдельных выражений или кардинальность каждого выражения не ограничены.

## <a name="remarks"></a>Remarks
  
  Когда в запросе используется оговорка GROUP BY, оговорка SELECT может содержать только подмножество свойств и системных функций, включенных в положение GROUP BY. Одним из исключений являются [агрегированные системные функции,](sql-query-aggregates.md)которые могут отображаться в оговорке SELECT без включения в положение GROUP BY. Вы также всегда можете включить буквальные значения в положение SELECT.

  Положение GROUP BY должно быть после пункта SELECT, FROM и WHERE и до положения OFFSET LIMIT. В настоящее время вы не можете использовать GROUP BY с оговоркой ORDER BY, но это планируется.

  Положение GROUP BY не допускает ни одного из следующих:
  
- Псевдоним свойства или функции системы псевдонимов (псевдоним по-прежнему допускается в рамках положения SELECT)
- Вложенные запросы
- Агрегатные функции системы (они разрешены только в пункте SELECT)

## <a name="examples"></a>Примеры

В этих примерах используется набор данных о питании, доступный через [игровую площадку Azure Cosmos DB.](https://www.documentdb.com/sql/demo)

Например, вот запрос, который возвращает общее количество товаров в каждой группе питания:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Некоторые результаты (ключевое слово TOP используется для ограничения результатов):

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Этот запрос имеет два выражения, используемые для разделения результатов:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Некоторые результаты:

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Этот запрос имеет системную функцию в оговорке GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Некоторые результаты:

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

Этот запрос использует как ключевые слова, так и системные функции в выражении свойства элемента:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Результаты:

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы](sql-query-getting-started.md)
- [Оговорка SELECT](sql-query-select.md)
- [Агрегированные функции](sql-query-aggregates.md)
