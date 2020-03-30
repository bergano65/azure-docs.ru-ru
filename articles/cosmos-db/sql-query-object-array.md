---
title: Работа с массивами и объектами в Azure Cosmos DB
description: Изучите синтаксис s'L для создания массивов и объектов в Azure Cosmos DB. В этой статье также приводятся некоторые примеры для выполнения операций на объектах массива
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246556"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Работа с массивами и объектами в Azure Cosmos DB

Ключевой особенностью API Azure Cosmos DB S'L является создание массива и объектов.

## <a name="arrays"></a>Массивы

Можно построить массивы, как показано в следующем примере:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Результаты:

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

Вы также можете использовать [экспрессию ARRAY](sql-query-subquery.md#array-expression) для построения массива из результатов [subquery.](sql-query-subquery.md) Этот запрос получает все различные имена детей в массиве.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>Итерации

API сSL обеспечивает поддержку для итерации над массивами JSON, с новой конструкцией добавленной через [ключевое слово IN](sql-query-keywords.md#in) в источнике FROM. Рассмотрим следующий пример:

```sql
    SELECT *
    FROM Families.children
```

Результаты:

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

Следующий запрос выполняет итерацию `children` в `Families` контейнере. Массив вывода отличается от предыдущего запроса. Этот пример `children`разделяет сядки и выравнивает результаты на один массив:  

```sql
    SELECT *
    FROM c IN Families.children
```

Результаты:

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

Вы можете фильтровать дальше на каждой отдельной записи массива, как показано в следующем примере:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Результаты:

```json
    [{
      "givenName": "Lisa"
    }]
```

Можно также агрегировать результат итерации массива. Например, в следующем запросе учитывается количество детей среди всех семей:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Результаты:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы](sql-query-getting-started.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Соединения](sql-query-join.md)