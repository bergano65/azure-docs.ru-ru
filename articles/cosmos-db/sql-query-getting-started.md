---
title: Начало работы с запросами SQL в Azure Cosmos DB
description: Введение в запросы SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 87b275806c06443e37e9e92c35a38b4cde378322
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342485"
---
# <a name="getting-started-with-sql-queries"></a>Приступая к работе с SQL-запросов

Azure учетные записи Cosmos DB SQL API поддерживает запросы элементов, с помощью Structured Query Language (SQL) как языка запросов JSON. Цели разработки языка запросов Azure Cosmos DB, чтобы:

* Поддерживает SQL, один из наиболее знакомы и популярные языки запросов, вместо того чтобы изобретать новый язык запросов. SQL предоставляет формальную модель программирования для выполнения многофункциональных запросов над элементы JSON.  

* Используйте модель программирования JavaScript в как основа для языка запросов. Система типов, вычисления выражений и вызовов функций JavaScript являются корни API-интерфейса SQL. Эти корни предоставляют естественную модель программирования для функций, как реляционных проекций, иерархической навигации среди элементов JSON, самосоединений пространственных запросов и вызов определяемых пользователем функций (UDF), написанных полностью на JavaScript.

## <a name="upload-sample-data"></a>Отправка примера данных

В учетной записи SQL API Cosmos DB, создайте контейнер с именем `Families`. Создайте два простых элемента JSON в контейнере. Можно запускать большинство образцов запросов в документации по Azure Cosmos DB запроса с помощью этого набора данных.

### <a name="create-json-items"></a>Создание элементов JSON

Следующий код создает два простых элемента JSON о семейств. Простые элементы JSON для семейства Андерсен и Wakefield включают родительских, дочерних и домашних животных, адрес и сведения о регистрации. Первый элемент имеет строки, числа, логические значения, массивы и вложенные свойства.


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

Второй элемент использует `givenName` и `familyName` вместо `firstName` и `lastName`.

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

Попробуем сформировать несколько запросов к данным JSON, чтобы понять некоторые ключевые аспекты языка запросов SQL Azure Cosmos DB.

Следующий запрос возвращает элементы где `id` поле соответствует `AndersenFamily`. Так как это `SELECT *` запрос, выходные данные запроса — это полный элемент JSON. Дополнительные сведения о синтаксис SELECT см. в разделе [инструкции SELECT](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Ниже приведены результаты запроса. 

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

Следующий запрос изменяет формат выходных данных JSON в другую форму. Запрос проецирует новые JSON `Family` объект с двумя выбранными полями, `Name` и `City`, когда адрес города совпадает со значением состояния. «Нью-ЙОРК, Нью-ЙОРК» соответствует этот случай.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Ниже приведены результаты запроса.

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Следующий запрос возвращает все заданные имена детей в семье, `id` соответствует `WakefieldFamily`, упорядоченные по городу.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Получаются такие результаты:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Примечания

В предыдущих примерах показано несколько аспектов языка запросов Cosmos DB:  

* SQL API работает со значениями JSON, имеет дело с сущностями древовидной вместо строк и столбцов. Можно ссылаться на узлы дерева любой произвольной вложенности, например `Node1.Node2.Node3…..Nodem`, аналогичную ссылку на двух частей `<table>.<column>` в ANSI SQL.

* Так, как работает язык запросов с помощью LINQ к бессхемным данным, динамически должен быть привязан к системе типов. Одно и то же выражение может возвращать различные типы у разных элементов. Результатом запроса является допустимым значением JSON, но не обязательно иметь фиксированную схему.  

* Azure Cosmos DB поддерживает только элементы, строго соответствующие JSON. Система типов и выражения могут обрабатывать только с типами JSON. Дополнительные сведения см. в разделе [спецификации JSON](https://www.json.org/).  

* Контейнер Cosmos DB является коллекцией элементов JSON, не имеющей схемы. Отношения внутри и между элементами контейнера неявно захвачены сдерживанием, не по первичный ключ и внешними ключами. Эта возможность важна для соединений внутри элемента, который обсуждается далее в этой статье.

## <a name="next-steps"></a>Дальнейшие действия

- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Предложение SELECT](sql-query-select.md)
