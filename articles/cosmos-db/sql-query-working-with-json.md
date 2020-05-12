---
title: Работа с JSON в Azure Cosmos DB
description: Узнайте, как запрашивать и получать доступ к вложенным свойствам JSON и использовать специальные символы в Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: d0b11cdb0cf2719b576b7a4c4f3fa534ae09dfa8
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117025"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Работа с JSON в Azure Cosmos DB

В API-интерфейсе SQL Azure Cosmos DB (Core) элементы хранятся в формате JSON. Система типов и выражения могут работать только с типами JSON. Дополнительные сведения см. в [спецификации JSON](https://www.json.org/).

Ниже приведены некоторые важные аспекты работы с JSON.

- Объекты JSON всегда начинаются с `{` левой скобки и заканчиваются `}` правой фигурной скобкой
- Свойства JSON можно [вложить](#nested-properties) в друг друга
- Значения свойств JSON могут быть массивами
- Имена свойств JSON чувствительны к регистру
- Имя свойства JSON может быть любым строковым значением (включая пробелы или символы, которые не являются буквами).

## <a name="nested-properties"></a>Вложенные свойства

Доступ к вложенному JSON можно получить с помощью метода доступа к точке. В запросах можно использовать вложенные свойства JSON так же, как и любые другие свойства.

Вот документ с вложенным JSON:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

В этом случае `state` `country` свойства, и `city` все вложены в `address` свойство.

В следующем примере проецируется два вложенных свойства: `f.address.state` и `f.address.city` .

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Результаты:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>Работа с массивами

Кроме вложенных свойств, JSON также поддерживает массивы.

Ниже приведен пример документа с массивом.

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

При работе с массивами можно получить доступ к определенному элементу в массиве, обратившись к его положению:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

Однако в большинстве случаев при работе с массивами будет использоваться [вложенный запрос](sql-query-subquery.md) или [самосоединение](sql-query-join.md) .

Например, ниже приведен документ, в котором показан Ежедневный баланс банковского счета клиента.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Если вы хотите выполнить запрос, в котором были показаны все клиенты с отрицательным балансом в некоторый момент, можно использовать [Exists](sql-query-subquery.md#exists-expression) с вложенным запросом:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="reserved-keywords-and-special-characters-in-json"></a>Зарезервированные ключевые слова и специальные символы в JSON

Доступ к свойствам можно получить с помощью оператора заключенного в кавычки свойства `[]` . Например, выражение `SELECT c.grade` and `SELECT c["grade"]` являются эквивалентными. Этот синтаксис полезен для экранирования свойства, которое содержит пробелы, Специальные символы или имеет то же имя, что и ключевое слово SQL или зарезервированное слово.

Например, вот документ со свойством с именем `order` и свойством `price($)` , содержащим специальные символы:

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

При выполнении запросов, включающих `order` свойство или `price($)` свойство, вы получите синтаксическую ошибку.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

Результат:

`
Syntax error, incorrect syntax near 'order'
`

Вы должны переписать те же запросы, что и ниже:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>Выражения JSON

Проекция также поддерживает выражения JSON, как показано в следующем примере:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Результаты:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

В предыдущем примере `SELECT` предложение должно создать объект JSON, а так как в примере нет ключа, предложение использует неявное имя переменной аргумента `$1` . Следующий запрос возвращает две неявные переменные аргумента: `$1` и `$2` .

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Результаты:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>Псевдонимы

В запросах можно явно задавать псевдонимы значений. Если запрос содержит два свойства с одинаковым именем, используйте псевдонимы для переименования одного или обоих свойств, чтобы они были неоднозначной в прогнозируемом результате.

### <a name="examples"></a>Примеры

`AS`Ключевое слово, используемое для присвоения псевдонимов, является необязательным, как показано в следующем примере при проецировании второго значения на `NameInfo` :

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Результаты:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Присвоение псевдонимов зарезервированным ключевым словам или специальным символам

Нельзя использовать псевдонимы для проецирования значения в качестве имени свойства с пробелом, специальным символом или зарезервированным словом. Если вы хотите изменить проекцию значения, например, на имя свойства с пробелом, можно использовать [выражение JSON](#json-expressions).

Ниже приведен пример:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Дальнейшие действия

- [Приступая к работе](sql-query-getting-started.md)
- [Предложение SELECT](sql-query-select.md)
- [Предложение WHERE](sql-query-where.md)
