---
title: Предложение GROUP BY в Azure Cosmos DB
description: Дополнительные сведения о предложении GROUP BY для Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: d92e24836a0eb5757de9bbdb516be290456deb7f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333265"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Предложение GROUP BY в Azure Cosmos DB

Предложение GROUP BY разделяет результаты запроса в соответствии со значениями одного или нескольких указанных свойств.

> [!NOTE]
> Azure Cosmos DB в настоящее время поддерживает GROUP BY в [пакете SDK для .net 3,3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.3.0) или более поздней версии.
> Поддержка других языковых пакетов SDK и портала Azure в настоящее время недоступна, но планируется.

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
  
   Допускается любое скалярное выражение, за исключением скалярных вложенных запросов и скалярных статистических выражений. Каждое скалярное выражение должно содержать по крайней мере одну ссылку на свойство. Количество отдельных выражений или количество элементов в каждом выражении не ограничено.

## <a name="remarks"></a>Примечания
  
  Если в запросе используется предложение GROUP BY, предложение SELECT может содержать только подмножество свойств и системных функций, входящих в предложение GROUP BY. Единственным исключением являются [статистические системные функции](sql-query-aggregates.md), которые могут присутствовать в предложении SELECT без включения в предложение GROUP BY. В предложение SELECT можно также включить литеральные значения.

  Предложение GROUP BY должно быть после предложения SELECT, FROM и WHERE и перед предложением предельного значения смещения. В настоящее время нельзя использовать GROUP BY с предложением ORDER BY, но это запланировано.

  Предложение GROUP BY не допускает выполнения следующих действий:
  
- Присвоение псевдонимов свойствам и присвоение псевдонимам системных функций (псевдонимы по-прежнему разрешены в предложении SELECT)
- Вложенные запросы
- Агрегатные системные функции (разрешены только в предложении SELECT)

## <a name="examples"></a>Примеры

В этих примерах используется набор данных информации питании, доступный в [Площадка для тестирования запросов Azure Cosmos DB](https://www.documentdb.com/sql/demo).

Например, Вот запрос, возвращающий общее количество элементов в каждой Фудграуп:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Некоторые результаты — (для ограничения результатов используется ключевое слово TOP):

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

В этом запросе используются два выражения для деления результатов:

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

Этот запрос содержит системную функцию в предложении GROUP BY:

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

В выражении свойства Item в этом запросе используются ключевые слова и системные функции:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Получаются такие результаты:

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
- [Предложение SELECT](sql-query-select.md)
- [Агрегатные функции](sql-query-aggregates.md)
