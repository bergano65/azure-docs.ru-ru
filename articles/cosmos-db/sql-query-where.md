---
title: ГДЕ положение в Azure Космос DB
description: Узнайте о статье S'L WHERE для Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898776"
---
# <a name="where-clause-in-azure-cosmos-db"></a>ГДЕ положение в Azure Космос DB

В дополнительном`WHERE <filter_condition>`положении WHERE () указывается условие (ы), которое исходные элементы JSON должны удовлетворять для запроса, чтобы включить их в результаты. Элемент JSON должен оценить `true` указанные условия, которые должны быть рассмотрены для результата. Слой индекса использует положение WHERE для определения наименьшего поднабора исходных элементов, которые могут быть частью результата.
  
## <a name="syntax"></a>Синтаксис
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Аргументы

- `<filter_condition>`  
  
   Указывает рекомендованное условие для возвращаемых документов.  
  
- `<scalar_expression>`  
  
   Выражение, представляющее вычисляемое значение. Подробнее о [выражениях Scalar.](sql-query-scalar-expressions.md)  
  
## <a name="remarks"></a>Remarks
  
  Чтобы вернуть документ, выражение, указанное в качестве условия фильтра, должно иметь значение true. Только значение `true` Boolean удовлетворит условие, любое другое значение: неопределенный, нулевой, ложный, номер, массив или объект не удовлетворит условие.

  Если ключ раздела в `WHERE` статью будет включен в раздельный фильтр, ваш запрос автоматически отфильтрует только соответствующие разделы.

## <a name="examples"></a>Примеры

Следующие запросы запросов `id` элементов, `AndersenFamily`содержащих свойство, значение которого является . Он исключает любой элемент, `id` который не имеет свойства `AndersenFamily`или стоимость которого не совпадает.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Результаты:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Скалярные выражения в оговорке WHERE

В предыдущем примере показан простой запрос с условием равенства. API S'L также поддерживает различные [скалярные выражения.](sql-query-scalar-expressions.md) Наиболее часто используются бинарные и унарные выражения. Ссылки на свойства исходного объекта JSON также являются допустимыми выражениями.

Вы можете использовать следующие поддерживаемые бинарные операторы:  

|**Тип оператора**  | **Значения** |
|---------|---------|
|Арифметические | +,-,*,/,% |
|Побитовые    | \|, &, ^, <<, >>, >>> (сдвиг вправо с заполнением нулями) |
|Логические    | AND, OR, NOT      |
|Сравнение | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Строка     |  \|\| (объединение) |

Следующие запросы используют бинарные операторы:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Вы также можете использовать неонаряемых операторов «,-,, а не в запросах, как показано в следующих примерах:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Вы также можете использовать ссылки на свойства в запросах. Например, `SELECT * FROM Families f WHERE f.isRegistered` возвращает сятное `isRegistered` элемент jSON, содержащий свойство со значением, `true`равным. Любое другое `false`значение, `Undefined` `<number>`например, `<string>` `<array>`, `null`, , `<object>`, , или , исключает элемент из результата.

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы](sql-query-getting-started.md)
- [Ключевое слово IN](sql-query-keywords.md#in)
- [ИЗ ПУНКТА](sql-query-from.md)