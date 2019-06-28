---
title: Предложение WHERE в Azure Cosmos DB
description: Дополнительные сведения о предложении SQL WHERE для Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 6a942e48ffea7785fe971cc2f8fa66e8569ed672
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342795"
---
# <a name="where-clause"></a>Предложение WHERE

Необязательное предложение WHERE (`WHERE <filter_condition>`) указывает условия должны удовлетворять что исходных элементов JSON для запроса включить их в результатах. Элемент JSON должны иметь указанных условий `true` следует учитывать для результата. На уровне индекса использует предложение WHERE для определения наименьшего подмножества исходных элементов, которые могут быть часть результата.
  
## <a name="syntax"></a>Синтаксис
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Аргументы

- `<filter_condition>`  
  
   Указывает рекомендованное условие для возвращаемых документов.  
  
- `<scalar_expression>`  
  
   Выражение, представляющее вычисляемое значение. См. в разделе [скалярные выражения](sql-query-scalar-expressions.md) подробные сведения.  
  

## <a name="remarks"></a>Примечания
  
  Чтобы вернуть документ, выражение, указанное в качестве условия фильтра, должно иметь значение true. Только логическое значение true соответствует условию. Все остальные значения, например undefined, Null, false, число, массив, объект, не подходят. 

## <a name="examples"></a>Примеры

В следующем запросе элементы запросы, содержащие `id` свойство, значение которого равно `AndersenFamily`. Это исключит любой элемент, который не имеет `id` свойство или значение которого не соответствует `AndersenFamily`.

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

В предыдущем примере показан простой запрос с условием равенства. SQL API также поддерживает различные [скалярные выражения](sql-query-scalar-expressions.md). Наиболее часто используются бинарные и унарные выражения. Ссылки на свойства исходного объекта JSON также являются допустимыми выражениями.

Можно использовать следующие поддерживаемые бинарные операторы:  

|**Тип оператора**  | **Значения** |
|---------|---------|
|Арифметические | +,-,*,/,% |
|Побитовые    | \|, &, ^, <<, >>, >>> (сдвиг вправо с заполнением нулями) |
|Логические    | AND, OR, NOT      |
|Сравнение | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (объединение) |

В следующих запросах используется бинарные операторы:

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

Можно также использовать унарные операторы +,-, ~, а не в запросах, как показано в следующих примерах:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Можно также использовать ссылки на свойства в запросах. Например `SELECT * FROM Families f WHERE f.isRegistered` возвращает элемент JSON, содержащий свойство `isRegistered` со значением, равным `true`. Любое другое значение, например `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, или `<array>`, исключает элемент из результата. 

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы](sql-query-getting-started.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Предложение FROM](sql-query-from.md)