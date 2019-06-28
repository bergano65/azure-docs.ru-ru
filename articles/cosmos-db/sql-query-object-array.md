---
title: Работа с массивами и объектами в Azure Cosmos DB
description: Дополнительные сведения о массиве и создание объектов синтаксис SQL для Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 338f3b51edf38d20a963992e121b7e2dbd0c6873
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342700"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Работа с массивами и объектами в Azure Cosmos DB

Ключевой особенностью Azure Cosmos DB SQL API — Создание массива и объекта.

## <a name="arrays"></a>Массивы

Можно создать массивы, как показано в следующем примере:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Получаются такие результаты:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

Можно также использовать [выражение МАССИВА](sql-query-subquery.md#array-expression) создается массив из [вложенного запроса](sql-query-subquery.md) результаты. Этот запрос возвращает все различные заданные имена дочерних элементов в массиве.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Итерация

SQL API обеспечивает поддержку перебора массивов JSON, с добавленным новую конструкцию [IN-ключевое слово](sql-query-keywords.md#in) в исходное выражение FROM. В следующем примере:

```sql
    SELECT *
    FROM Families.children
```

Получаются такие результаты:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Следующий запрос выполняет итерацию по `children` в `Families` контейнера. Выходной массив отличается от предыдущего запроса. Этот пример разбивает `children`и собирает результаты в один массив:  

```sql
    SELECT *
    FROM c IN Families.children
```

Получаются такие результаты:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Вы можете отфильтровать далее в каждой конкретной записи массива, как показано в следующем примере:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Получаются такие результаты:

```json
    [{
      "givenName": "Lisa"
    }]
```

Можно также объединить над результатом итерации массива. Например следующий запрос подсчитывает число детей во всех семьях:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Получаются такие результаты:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы](sql-query-getting-started.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Соединения](sql-query-join.md)