---
title: Предложение SELECT в Azure Cosmos DB
description: Дополнительные сведения о предложении SQL SELECT для Azure Cosmos DB. Используйте SQL в качестве языка запросов JSON Azure Cosmos DB.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: d34b1c39d9789409dc365cd4cf07fdc3d5a780fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003522"
---
# <a name="select-clause"></a>Предложение SELECT

Каждый запрос состоит из предложения SELECT и необязательных предложений [from](sql-query-from.md) и [WHERE](sql-query-where.md) для стандартов ANSI SQL. Как правило, источник в предложении FROM является перечислимым, а предложение WHERE применяет фильтр к источнику для получения подмножества элементов JSON. Затем предложение SELECT проецирует запрошенные значения JSON в списке выбора.

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
  
  Указывает, что необходимо удалить дубликаты проецируемых свойств.  

- `<scalar_expression>`  

  Выражение, представляющее вычисляемое значение. Дополнительные сведения см. в разделе [Скалярные выражения](sql-query-scalar-expressions.md).  

## <a name="remarks"></a>Примечания

Если предложение FROM объявило один псевдоним, действителен только синтаксис `SELECT *`. Синтаксис `SELECT *` обеспечивает проекцию удостоверения, что может пригодиться, если проекция не требуется. Кроме того, это * единственный действительный синтаксис, если в предложении FROM указан один источник входных данных.  
  
`SELECT <select_list>` и `SELECT *` — это "синтаксический сахар". При необходимости их можно выразить с помощью простых инструкций SELECT, как показано ниже.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   эквивалентно выражению:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   эквивалентно выражению:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Примеры

Следующий пример запроса SELECT `address` возвращает из `Families` , чьи `id` соответствия `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Получаются такие результаты:

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
Доступ к свойствам можно получить с помощью оператора заключенного в кавычки свойства []. Например, выражение `SELECT c.grade` and `SELECT c["grade"]` являются эквивалентными. Этот синтаксис полезен для экранирования свойства, которое содержит пробелы, Специальные символы или имеет то же имя, что и ключевое слово SQL или зарезервированное слово.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Вложенные свойства

В следующем примере проецируется два вложенных `f.address.state` свойства `f.address.city`, и.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Получаются такие результаты:

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

Получаются такие результаты:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

В предыдущем примере предложение SELECT должно создать объект JSON, а поскольку в примере нет ключа, предложение использует неявное имя `$1`переменной аргумента. Следующий запрос возвращает две неявные переменные аргумента `$2`: `$1` и.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Получаются такие результаты:

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

## <a name="next-steps"></a>Следующие шаги

- [Начало работы](sql-query-getting-started.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Предложение WHERE](sql-query-where.md)