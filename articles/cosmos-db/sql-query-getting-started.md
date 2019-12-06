---
title: Начало работы с запросами SQL в Azure Cosmos DB
description: Узнайте, как использовать SQL запросы для запроса данных из Azure Cosmos DB. Образец данных можно передать в контейнер в Azure Cosmos DB и выполнить запрос.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873341"
---
# <a name="getting-started-with-sql-queries"></a>Приступая к работе с запросами SQL

Azure Cosmos DB учетные записи API SQL поддерживают запросы элементов с помощью язык SQL (SQL) в качестве языка запросов JSON. Цели проектирования языка запросов Azure Cosmos DB:

* Поддержка SQL, один из наиболее знакомых и популярных языков запросов вместо создания нового языка запросов. SQL предоставляет формальную модель программирования для сложных запросов по элементам JSON.  

* Используйте модель программирования JavaScript в качестве основы для языка запросов. Система типов JavaScript, вычисление выражений и вызов функции являются корнями API SQL. Эти корни предоставляют естественную модель программирования для таких функций, как реляционные проекции, иерархическая навигация по элементам JSON, самостоятельные соединения, пространственные запросы и вызов определяемых пользователем функций (UDF), написанных полностью на JavaScript.

## <a name="upload-sample-data"></a>Отправить образец данных

В учетной записи Cosmos DB API SQL создайте контейнер с именем `Families`. Создайте два простых элемента JSON в контейнере. Большинство примеров запросов можно выполнять в Azure Cosmos DB документах запросов, использующих этот набор данных.

### <a name="create-json-items"></a>Создание элементов JSON

Следующий код создает два простых элемента JSON о семействах. К простым элементам JSON для семейств Андерсен и Вейкфилд относятся родители, дочерние элементы и их pets, адрес и сведения о регистрации. Первый элемент содержит строки, числа, логические значения, массивы и вложенные свойства.


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

Попробуйте выполнить несколько запросов к данным JSON, чтобы понять некоторые ключевые аспекты языка SQL-запросов Azure Cosmos DB.

Следующий запрос возвращает элементы, в которых поле `id` соответствует `AndersenFamily`. Так как это `SELECT *` запрос, выходные данные запроса — это полный элемент JSON. Дополнительные сведения о синтаксисе SELECT см. в разделе [инструкция SELECT](sql-query-select.md). 

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

Следующий запрос переформатирует выходные данные JSON в другую форму. Запрос проецирует новый объект JSON `Family` с двумя выбранными полями, `Name` и `City`, если адресный город совпадает с состоянием. «Москва» соответствует этому варианту.

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

Следующий запрос возвращает все заданные имена дочерних элементов в семействе, `id` соответствует `WakefieldFamily`, упорядоченное по городу.

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

* Так как API SQL работает с значениями JSON, он взаимодействует с сущностями в форме дерева, а не со строками и столбцами. Вы можете ссылаться на узлы дерева в любой произвольной глубине, например `Node1.Node2.Node3…..Nodem`, аналогично ссылке из двух частей `<table>.<column>` в ANSI SQL.

* Поскольку язык запросов работает с данными без схем, система типов должна быть динамически привязана. Одно и то же выражение может возвращать различные типы у разных элементов. Результатом запроса является допустимое значение JSON, но оно не обязательно должно быть фиксированной схемой.  

* Azure Cosmos DB поддерживает только элементы, строго соответствующие JSON. Система типов и выражения могут работать только с типами JSON. Дополнительные сведения см. в [спецификации JSON](https://www.json.org/).  

* Контейнер Cosmos — это коллекция элементов JSON без схемы. Отношения внутри и между элементами контейнера неявно фиксируются с помощью включения, а не связей первичного и внешнего ключей. Эта функция важна для соединений внутри элемента, обсуждаемых далее в этой статье.

## <a name="next-steps"></a>Дальнейшие действия

- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Предложение SELECT](sql-query-select.md)
