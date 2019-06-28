---
title: Ключевые слова SQL для Azure Cosmos DB
description: Дополнительные сведения о ключевых словах SQL для Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342843"
---
# <a name="keywords-in-azure-cosmos-db"></a>Ключевые слова в Azure Cosmos DB
В этой статье описаны ключевые слова, которые могут быть использованы в запросах Azure Cosmos DB SQL.

## <a name="between"></a>МЕЖДУ

Как и в ANSI SQL можно использовать ключевое слово BETWEEN для выражения запросов к диапазонам строку или числовые значения. Например следующий запрос возвращает все элементы, в которых первый ребенок учится является 1-5, включительно.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

В отличие от ANSI SQL, можно также использовать предложение BETWEEN в предложении FROM, как показано в следующем примере.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

В API SQL, в отличие от ANSI SQL можно выразить запросы в диапазоне для свойств. Например `grade` может быть номер like `5` некоторые элементы и строкой как `grade4` в других случаях. В таких случаях, как в JavaScript, результатом сравнения между двумя разными типами `Undefined`, поэтому он пропускается.

> [!TIP]
> Сократить время выполнения запроса создайте политику индексации, который использует тип индекса диапазона для числовых свойств или путей, которые выполняют фильтрацию в предложении BETWEEN.

## <a name="distinct"></a>DISTINCT

Ключевое слово DISTINCT удаляет дубликаты в проекции запроса.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

В этом примере запрос проецирует значения для каждой фамилии.

Получаются такие результаты:

```json
[
    "Andersen"
]
```

Вы также можете проецировать уникальных объектов. В этом случае поле lastName не существует в одном из двух документов, поэтому запрос возвращает пустой объект.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Получаются такие результаты:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

Ключевое слово DISTINCT, также может использоваться в проекции во вложенном запросе:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Этот запрос проецирует массив, содержащий givenName каждый дочерний элемент с удаленными повторениями. Этот массив используется псевдоним ChildNames после чего во внешнем запросе.

Получаются такие результаты:

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
## <a name="in"></a> IN

Чтобы проверить, соответствует ли указанное значение любое значение в списке, используйте ключевое слово IN. Например, следующий запрос возвращает все элементы семейства где `id` — `WakefieldFamily` или `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

В следующем примере возвращаются все элементы, где состояние — это любые из указанных значений:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API обеспечивает поддержку для [перебора массивов JSON](sql-query-object-array.md#Iteration), с добавленным ключевым словом in в исходное выражение FROM новую конструкцию. 

## <a name="top"></a>ВВЕРХ

Ключевое слово TOP возвращаются первые `N` число результатов запроса в неопределенном порядке. Рекомендуется, использовать TOP с предложением ORDER BY для ограничения результатов к первому `N` число упорядоченные значения. Объединение этих двух предложениях является единственным способом, который позволяет предсказуемо указать которой строк влияет на верхний.

То можно использовать с постоянным значением, как показано в следующем примере, или с переменным значением посредством параметризованных запросов.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Получаются такие результаты:

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
- [Соединения](sql-query-join.md)
- [Вложенные запросы](sql-query-subquery.md)