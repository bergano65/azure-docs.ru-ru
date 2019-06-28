---
title: Предложение SELECT в Azure Cosmos DB
description: Дополнительные сведения о предложении SQL SELECT для Azure Cosmos DB. Использует SQL в качестве языка запросов Azure Cosmos DB JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 84d0212f7f212b4554b506726e027fe51f795eea
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342749"
---
# <a name="select-clause"></a>Предложение SELECT

Каждый запрос состоит из предложения SELECT и необязательных [FROM](sql-query-from.md) и [ГДЕ](sql-query-where.md) предложений по стандартам ANSI SQL. Как правило источник в предложении FROM перечисляется, и предложение WHERE применяется фильтр в источнике, для извлечения подмножества элементов JSON. Предложение SELECT затем проецирует запрошенных значений JSON в списке выбора.

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
  
  Указывает, что дубликаты прогнозируемых свойств должны быть удалены.  

- `<scalar_expression>`  

  Выражение, представляющее вычисляемое значение. Дополнительные сведения см. в разделе [Скалярные выражения](sql-query-scalar-expressions.md).  

## <a name="remarks"></a>Примечания

Если предложение FROM объявило один псевдоним, действителен только синтаксис `SELECT *`. Синтаксис `SELECT *` обеспечивает проекцию удостоверения, что может пригодиться, если проекция не требуется. Кроме того, это * единственный действительный синтаксис, если в предложении FROM указан один источник входных данных.  
  
`SELECT <select_list>` и `SELECT *` — это "синтаксический сахар". При необходимости их можно выразить с помощью простых инструкций SELECT, как показано ниже.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   эквивалентно правилу  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   эквивалентно правилу  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Примеры

Следующая инструкция SELECT запрос возвращает пример `address` из `Families` которого `id` соответствует `AndersenFamily`:

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
Свойства с помощью operator [] заключенного в кавычки свойства доступны. Например, выражение `SELECT c.grade` and `SELECT c["grade"]` являются эквивалентными. Этот синтаксис полезен для экранирования свойство, которое содержит пробелы, специальные символы или имеет имя, совпадающее с именем ключевого слова SQL или зарезервированное слово.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Вложенные свойства

В следующем примере проецируется два вложенных свойства `f.address.state` и `f.address.city`.

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

В приведенном выше примере предложение SELECT необходимо создавать объект JSON, и поскольку образец предоставляет без ключа, предложении используется имя переменной неявный аргумент `$1`. Следующий запрос возвращает две неявные переменные аргументов: `$1` и `$2`.

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

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы](sql-query-getting-started.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Предложение WHERE](sql-query-where.md)