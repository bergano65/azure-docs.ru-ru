---
title: Ключевые слова для Azure Cosmos DB
description: Узнайте о ключевых словах для Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: f2da2695ec20eac9dd2636104d3314427e60d541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498539"
---
# <a name="keywords-in-azure-cosmos-db"></a>Ключевые слова в Azure Космос DB

В этой статье подробно описаны ключевые слова, которые могут быть использованы в запросах Azure Cosmos DB S'L.

## <a name="between"></a>BETWEEN

Ключевое `BETWEEN` слово можно использовать для выражения запросов в отношении диапазонов строк или численных значений. Например, следующий запрос возвращает все элементы, в которых первый ребенок класс 1-5, включительно.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Ключевое слово `BETWEEN` в оговорке `SELECT` также можно использовать, как в следующем примере.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

В API СЗЛ, в отличие от ANSI S'L, вы можете выражать запросы диапазона в отношении свойств смешанных типов. Например, `grade` может быть `5` число, как в `grade4` некоторых элементах и строки, как в других. В этих случаях, как и в JavaScript, сравнение между двумя различными типами приводит `Undefined`к, так что элемент пропускается.

> [!TIP]
> Для более быстрого времени выполнения запроса создайте политику индексирования, которая использует тип `BETWEEN` индекса диапазона против любых числовых свойств или путей, которые фильтрует положение.

## <a name="distinct"></a>DISTINCT

Ключевое `DISTINCT` слово устраняет дубликаты в проекции запроса.

В этом примере запрос проектирует значения для каждой фамилии:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Результаты:

```json
[
    "Andersen"
]
```

Вы также можете проектировать уникальные объекты. В этом случае поле lastName не существует в одном из двух документов, поэтому запрос возвращает пустой объект.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Результаты:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

ОТЛИЧИЕ также может быть использован в проекции в рамках подвоза:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Этот запрос проецирует массив, содержащий данные каждого ребенка с дубликатами удалены. Этот массив известен как ChildNames и проецируется во внешнем запросе.

Результаты:

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

Запросы с функцией агрегированной `DISTINCT` системы и выемкой с не поддерживаются. Например, следующий запрос не поддерживается.

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Используйте ключевое слово IN, чтобы проверить, соответствует ли указанное значение какое-либо значение в списке. Например, следующий запрос возвращает все `id` семейные элементы, где находится `WakefieldFamily` или `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Следующий пример возвращает все элементы, в которых состояние является любым из указанных значений:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

API сS-L обеспечивает поддержку для [итерации над массивами JSON,](sql-query-object-array.md#Iteration)с новой конструкцией добавленной через ключевое слово в источнике FROM.

Если вы включите ключ `IN` раздела в фильтр, ваш запрос автоматически отфильтрует только соответствующие разделы.

## <a name="top"></a>TOP

Ключевое слово TOP `N` возвращает первое число результатов запроса в неопределенном порядке. В качестве наилучшей практики `ORDER BY` используйте TOP с `N` оговоркой, чтобы ограничить результаты первым числом упорядоченных значений. Объединение этих двух положений является единственным способом предсказуемо указать, какие строки влияет НА ТОП.

Можно использовать TOP с постоянным значением, как в следующем примере, или с переменным значением, используя параметризированные запросы.

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
- [Соединения](sql-query-join.md)
- [Вложенные запросы](sql-query-subquery.md)