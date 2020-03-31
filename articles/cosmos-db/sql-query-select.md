---
title: Пункт SELECT в Azure Cosmos DB
description: Узнайте о статье S'L SELECT для Azure Cosmos DB. Используйте s'L в качестве языка запросов Azure Cosmos DB JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469941"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Пункт SELECT в Azure Cosmos DB

Каждый запрос состоит из положения SELECT и дополнительных положений [FROM](sql-query-from.md) и [WHERE,](sql-query-where.md) в соответствии со стандартами ANSI S'L. Как правило, источник в оговорке FROM перечисляется, и пункт WHERE применяет фильтр на источник для получения подмножества элементов JSON. Затем положение SELECT проецирует запрашиваемые значения JSON в список выбора.

## <a name="syntax"></a>Синтаксис

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>Аргументы
  
- `<select_specification>`  

  Свойства или значения, выбираемые для результирующего набора.  
  
- `'*'`  

  Указывает, что значение необходимо извлечь без внесения изменений. В частности, если обработанное значение — это объект, извлекаются все свойства.  
  
- `<object_property_list>`  
  
  Указывает список свойств, которые требуется извлечь. Каждое возвращаемое значение представляет собой объект с указанными свойствами.  
  
- `VALUE`  

  Указывает, что следует извлечь только значение JSON, а не весь объект JSON. Этот аргумент, в отличие от аргумента `<property_list>`, не выделяет прогнозируемое значение в объекте.  
 
- `DISTINCT`
  
  Уточняется, что дубликаты прогнозируемых свойств должны быть удалены.  

- `<scalar_expression>`  

  Выражение, представляющее вычисляемое значение. Дополнительные сведения см. в разделе [Скалярные выражения](sql-query-scalar-expressions.md).  

## <a name="remarks"></a>Remarks

Если предложение FROM объявило один псевдоним, действителен только синтаксис `SELECT *`. Синтаксис `SELECT *` обеспечивает проекцию удостоверения, что может пригодиться, если проекция не требуется. Кроме того, это * единственный действительный синтаксис, если в предложении FROM указан один источник входных данных.  
  
`SELECT <select_list>` и `SELECT *` — это "синтаксический сахар". При необходимости их можно выразить с помощью простых инструкций SELECT, как показано ниже.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   эквивалентно правилу  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   эквивалентно правилу  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Примеры

Следующий пример запроса `address` `Families` SELECT `id` `AndersenFamily`возвращается из совпадений:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Результаты:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>Метод доступа к свойству, заключенному в кавычки
Вы можете получить доступ к свойствам с помощью указанного оператора свойств. Например, выражение `SELECT c.grade` and `SELECT c["grade"]` являются эквивалентными. Этот синтаксис полезен для избежания свойства, содержащего пробелы, специальные символы или с тем же названием, что и ключевое слово или зарезервированное слово.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Вложенные свойства

Следующий пример проецирует `f.address.state` два `f.address.city`вложенных свойства, и .

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
### <a name="json-expressions"></a>Выражения JSON

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

В предыдущем примере оговорке SELECT необходимо создать объект JSON, и, поскольку в выборке нет ключа, в оговорке используется переменное имя `$1`неявного аргумента. Следующий запрос возвращает две неявные переменные аргумента: `$1` и `$2`.

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
## <a name="reserved-keywords-and-special-characters"></a>Зарезервированные ключевые слова и специальные символы

Если ваши данные содержат свойства с теми же именами, что и зарезервированные ключевые слова, такие как "заказ" или "Группа", то запросы против этих документов приведут к ошибкам синтаксиса. Для успешного выполнения запроса необходимо учесть свойство в `[]` характере.

Например, вот документ с именем `order` свойства `price($)` и свойством, содержащим специальные символы:

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

Если вы запустите запросы, включающие `order` свойство или `price($)` свойство, вы получите ошибку синтаксиса.

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

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы](sql-query-getting-started.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ПОЛОЖЕНИЕ WHERE](sql-query-where.md)
