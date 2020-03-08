---
title: Предложение WHERE в Azure Cosmos DB
description: Дополнительные сведения о предложении SQL WHERE для Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898776"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Предложение WHERE в Azure Cosmos DB

Необязательное предложение WHERE (`WHERE <filter_condition>`) указывает условия, которым должны соответствовать исходные элементы JSON, чтобы запрос включал их в результаты. Элемент JSON должен оценивать указанные условия для `true`, чтобы считаться результатом. Слой индекса использует предложение WHERE для определения наименьшего подмножества исходных элементов, которые могут быть частью результата.
  
## <a name="syntax"></a>Синтаксис
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Аргументы

- `<filter_condition>`  
  
   Указывает рекомендованное условие для возвращаемых документов.  
  
- `<scalar_expression>`  
  
   Выражение, представляющее вычисляемое значение. Дополнительные сведения см. в разделе [скалярные выражения](sql-query-scalar-expressions.md) .  
  
## <a name="remarks"></a>Примечания
  
  Чтобы вернуть документ, выражение, указанное в качестве условия фильтра, должно иметь значение true. Только логическое значение `true` будет соответствовать условию, любое другое значение: undefine, null, false, Number, Array или Object не будет соответствовать условию.

  Если включить ключ секции в предложение `WHERE` как часть фильтра равенства, запрос будет автоматически фильтровать только соответствующие секции.

## <a name="examples"></a>Примеры

Следующий запрос запрашивает элементы, содержащие свойство `id`, значение которого равно `AndersenFamily`. Он исключает любой элемент, не имеющий свойства `id` или, значение которого не совпадает с `AndersenFamily`.

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

### <a name="scalar-expressions-in-the-where-clause"></a>Скалярные выражения в предложении WHERE

В предыдущем примере показан простой запрос с условием равенства. API SQL также поддерживает различные [скалярные выражения](sql-query-scalar-expressions.md). Наиболее часто используются бинарные и унарные выражения. Ссылки на свойства исходного объекта JSON также являются допустимыми выражениями.

Можно использовать следующие поддерживаемые бинарные операторы:  

|**Тип оператора**  | **Значения** |
|---------|---------|
|Арифметические | +,-,*,/,% |
|Побитовые операторы    | \|, &, ^, <<, >>, >>> (сдвиг вправо с заполнением нулями) |
|Логические    | AND, OR, NOT      |
|Сравнение | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (объединение) |

В следующих запросах используются бинарные операторы:

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

Кроме того, можно использовать унарные операторы +,-, ~, а не в запросах, как показано в следующих примерах:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

В запросах также можно использовать ссылки на свойства. Например, `SELECT * FROM Families f WHERE f.isRegistered` Возвращает элемент JSON, содержащий свойство `isRegistered` со значением, равным `true`. Любое другое значение, например `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`или `<array>`, исключает элемент из результата.

## <a name="next-steps"></a>Следующие шаги

- [Начало работы](sql-query-getting-started.md)
- [Ключевое слово IN](sql-query-keywords.md#in)
- [Предложение FROM](sql-query-from.md)