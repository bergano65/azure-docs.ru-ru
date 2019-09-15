---
title: Предложение WHERE в Azure Cosmos DB
description: Дополнительные сведения о предложении SQL WHERE для Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 362024868de269ed64a440a25e8c19c5b68bef80
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003476"
---
# <a name="where-clause"></a>Предложение WHERE

Необязательное предложение Where`WHERE <filter_condition>`() указывает условия, которым должны соответствовать исходные элементы JSON, чтобы запрос включал их в результаты. Элемент JSON должен оценивать указанные условия `true` , чтобы считаться результатом. Слой индекса использует предложение WHERE для определения наименьшего подмножества исходных элементов, которые могут быть частью результата.
  
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
  
  Чтобы вернуть документ, выражение, указанное в качестве условия фильтра, должно иметь значение true. Только логическое значение true соответствует условию. Все остальные значения, например undefined, Null, false, число, массив, объект, не подходят. 

## <a name="examples"></a>Примеры

Следующий запрос запрашивает элементы, содержащие `id` свойство со `AndersenFamily`значением. Он исключает любой элемент, не имеющий `id` свойства или значения которого не совпадают. `AndersenFamily`

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Получаются такие результаты:

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
|Побитовые    | \|, &, ^, <<, >>, >>> (сдвиг вправо с заполнением нулями) |
|Логические    | AND, OR, NOT      |
|Сравнение | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Строковое     |  \|\| (объединение) |

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

В запросах также можно использовать ссылки на свойства. Например, `SELECT * FROM Families f WHERE f.isRegistered` Возвращает элемент JSON, содержащий свойство `isRegistered` со значением, равным `true`. Любое `false`другое значение, например `Undefined`, `null` `<number>` ,,`<object>`, ,или`<array>`, исключает элемент из результата. `<string>` 

## <a name="next-steps"></a>Следующие шаги

- [Начало работы](sql-query-getting-started.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Предложение FROM](sql-query-from.md)