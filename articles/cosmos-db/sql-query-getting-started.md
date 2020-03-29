---
title: Начало работы с запросами s'L в Azure Cosmos DB
description: Узнайте, как использовать запросы S'L для запроса данных из Azure Cosmos DB. Вы можете загрузить выборочные данные в контейнер в Azure Cosmos DB и задать запрос.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873341"
---
# <a name="getting-started-with-sql-queries"></a>Getting started with SQL queries (Начало работы с запросами SQL)

Учетные записи Azure Cosmos DB S'L API поддерживают элементы запроса, используя структурированный язык запросов (S'L) в качестве языка запросов JSON. Цели проектирования языка запросов Azure Cosmos DB заключаются в:

* Поддержка S'L, одного из самых знакомых и популярных языков запросов, вместо того, чтобы изобретать новый язык запросов. СЗЛ предоставляет официальную модель программирования для богатых запросов по элементам JSON.  

* Используйте модель программирования JavaScript в качестве основы для языка запросов. Система типов JavaScript, оценка выражения и вызов функции являются корнями API. Эти корни обеспечивают естественную модель программирования для таких функций, как реляционные проекции, иерархическая навигация по элементам JSON, самосоединения, пространственные запросы и вызов пользовательских функций (UDF), написанных полностью в JavaScript.

## <a name="upload-sample-data"></a>Загрузка выборочных данных

В вашей учетной записи S'L API Cosmos DB создайте контейнер под названием `Families`. Создайте два простых элемента JSON в контейнере. С помощью этого набора данных можно выполнить большинство выборочных запросов в документах запросов запросов Azure Cosmos DB.

### <a name="create-json-items"></a>Создание элементов JSON

Следующий код создает два простых элемента JSON о семьях. Простые предметы JSON для семей Андерсен и Уэйкфилд включают родителей, детей и их домашних животных, адрес и регистрационную информацию. Первый элемент имеет строки, числа, булеаны, массивы и вложенные свойства.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Второй пункт `givenName` использует `familyName` и `firstName` `lastName`вместо и .

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Запрос элементов JSON

Попробуйте несколько запросов в отношении данных JSON, чтобы понять некоторые ключевые аспекты языка запросов Azure Cosmos DB.

Следующий запрос возвращает элементы, `AndersenFamily`в которых совпадает `id` поле. Поскольку это `SELECT *` запрос, вывод запроса является полным элементом JSON. Для получения дополнительной информации [SELECT statement](sql-query-select.md)о синтаксисе SELECT см. 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Результаты запроса: 

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

Следующий запрос переформатирует выход JSON в другую форму. Запрос проецирует новый `Family` объект JSON с `Name` `City`двумя выбранными полями, и, когда адресный город такой же, как состояние. "NY, NY" совпадает с этим делом.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Результаты запроса:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Следующий запрос возвращает все имена детей в `id` семье, чьи матчи, `WakefieldFamily`заказанные городом.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Результаты:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Remarks

Предыдущие примеры показывают несколько аспектов языка запроса Cosmos DB:  

* Так как API с помощью S'L работает на значениях JSON, он занимается сущностями в форме дерева, а не строками и столбцов. Вы можете ссылаться на узлы дерева `Node1.Node2.Node3…..Nodem`на любой произвольной `<table>.<column>` глубине, например, на ссылку из двух частей в ANSI S'L.

* Поскольку язык запросов работает с бессхематичными данными, система типа должна быть связана динамически. Одно и то же выражение может возвращать различные типы у разных элементов. Результатом запроса является допустимое значение JSON, но не гарантируется фиксированная схема.  

* Azure Cosmos DB поддерживает только элементы, строго соответствующие JSON. Система типов и выражения ограничены только типами JSON. Для получения дополнительной [информации](https://www.json.org/)см.  

* Контейнер Cosmos представляет собой коллекцию предметов JSON без схемы. Отношения внутри и между контейнерными пунктами неявно фиксируются сдерживанием, а не основными ключевыми и внешними ключевыми отношениями. Эта функция важна для внутриэлементных соединений, обсуждаемых позднее в этой статье.

## <a name="next-steps"></a>Дальнейшие действия

- [Введение в Azure Космос DB](introduction.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Оговорка SELECT](sql-query-select.md)
