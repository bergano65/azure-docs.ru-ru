---
title: Ключевые слова SQL для Azure Cosmos DB
description: Сведения о ключевых словах SQL для Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: f00e757f9b51da850c49924f6ae49bf00c9c53d1
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496687"
---
# <a name="keywords-in-azure-cosmos-db"></a>Ключевые слова в Azure Cosmos DB

В этой статье описываются ключевые слова, которые могут использоваться в запросах Azure Cosmos DB SQL.

## <a name="between"></a>BETWEEN

`BETWEEN`Для выражения запросов к диапазонам строковых или числовых значений можно использовать ключевое слово. Например, следующий запрос возвращает все элементы, в которых уровень первого дочернего элемента — 1-5 включительно.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Можно также использовать `BETWEEN` ключевое слово в `SELECT` предложении, как показано в следующем примере.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

В отличие от ANSI SQL, в API SQL можно выразить диапазон запросов к свойствам смешанных типов. Например, `grade` может быть числом, похожим `5` на некоторые элементы, и строкой, как `grade4` в других. В таких случаях, как в JavaScript, сравнение двух разных типов приводит к результату `Undefined` , поэтому элемент пропускается.

## <a name="distinct"></a>DISTINCT

`DISTINCT`Ключевое слово удаляет дубликаты в проекции запроса.

В этом примере запрос Проецирует значения для каждого фамилии:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Вы получите такие результаты:

```json
[
    "Andersen"
]
```

Можно также проецировать уникальные объекты. В этом случае поле lastName не существует в одном из двух документов, поэтому запрос возвращает пустой объект.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Вы получите такие результаты:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT`также может использоваться в проекции вложенного запроса:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Этот запрос проецирует массив, который содержит все givenName потомков с удаленными дубликатами. Этот массив имеет псевдоним в виде Чилднамес и проецируется во внешнем запросе.

Вы получите такие результаты:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Не поддерживаются запросы с агрегатной системной функцией и вложенным запросом с `DISTINCT`. Например, следующий запрос выполнить не удастся:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Используйте ключевое слово IN, чтобы проверить, соответствует ли указанное значение любому значению в списке. Например, следующий запрос возвращает все элементы семейства, где `id` — `WakefieldFamily` или `AndersenFamily` .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

В следующем примере возвращаются все элементы, в которых состояние — любое из указанных значений:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

API SQL обеспечивает поддержку [итерации по массивам JSON](sql-query-object-array.md#Iteration)с новой конструкцией, добавленной с помощью ключевого слова in из источника from.

Если включить ключ секции в `IN` фильтр, запрос будет автоматически фильтроваться только на соответствующие секции.

## <a name="top"></a>В начало

Ключевое слово TOP возвращает первое `N` число результатов запроса в неопределенном порядке. Рекомендуется использовать TOP с `ORDER BY` предложением для ограничения результатов до первого `N` числа упорядоченных значений. Объединение этих двух предложений является единственным способом прогнозируемого указания того, какие строки на самом верху влияют.

TOP можно использовать с постоянным значением, как в следующем примере, или с переменным значением с помощью параметризованных запросов.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Результаты:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы](sql-query-getting-started.md)
- [Joins](sql-query-join.md)
- [Вложенные запросы](sql-query-subquery.md)