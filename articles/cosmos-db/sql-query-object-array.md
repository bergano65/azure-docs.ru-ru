---
title: Работа с массивами и объектами в Azure Cosmos DB
description: Сведения о синтаксисе SQL для создания массивов и объектов для Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 17a0e4ddf5acd267a4cfbb68c218fe9409a91d57
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003921"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Работа с массивами и объектами в Azure Cosmos DB

Ключевой функцией API Azure Cosmos DB SQL является создание массивов и объектов.

## <a name="arrays"></a>Массивы

Можно создавать массивы, как показано в следующем примере:

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

Можно также использовать [выражение массива](sql-query-subquery.md#array-expression) для создания массива из результатов [вложенного запроса](sql-query-subquery.md) . Этот запрос получает все уникальные заданные имена дочерних элементов в массиве.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Итерация

API SQL обеспечивает поддержку итерации по массивам JSON с новой конструкцией, добавленной с помощью [ключевого слова in](sql-query-keywords.md#in) из источника from. В следующем примере:

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

Следующий запрос выполняет итерацию `children` `Families` в контейнере. Выходной массив отличается от предыдущего запроса. В этом примере показано `children`разбиение и сведение результатов в один массив:  

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

Можно выполнить фильтрацию для каждой отдельной записи массива, как показано в следующем примере:

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

Можно также выполнить статистическую обработку по результату итерации массива. Например, следующий запрос подсчитывает количество дочерних элементов во всех семействах:

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

## <a name="next-steps"></a>Следующие шаги

- [Начало работы](sql-query-getting-started.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Соединения](sql-query-join.md)