---
title: Функции, определяемые пользователями (UDF) в Azure Cosmos DB
description: Узнайте о функциях, определяемых пользователями, в Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614330"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Функции, определяемые пользователями (UDF) в Azure Cosmos DB

API с помощью S'L обеспечивает поддержку функций, определяемых пользователями (UDF). С scalar UDFs, вы можете пройти в ноль или много аргументов и вернуть один результат аргумента. API проверяет каждый аргумент на наличие юридических значений JSON.  

API расширяет синтаксис S'L для поддержки логики пользовательских приложений с помощью UDFs. Вы можете зарегистрировать UDF с помощью API и ссылаться на них в запросах S'L. На самом деле пользовательские функции специально предназначены для вызовов из запросов. В качестве следствия, UDF не имеют доступа к объекту контекста, как и другие типы JavaScript, такие как сохраненные процедуры и триггеры. Запросы читаются только на первичных или вторичных репликах. UDFs, в отличие от других типов JavaScript, предназначены для выполнения вторичных реплик.

Следующий пример регистрирует UDF под контейнером элемента в базе данных Космос. Пример создает UDF, чье `REGEX_MATCH`имя . Он принимает два значения строки `input` `pattern`JSON и проверяет, соответствует ли первая модель, `string.match()` указанная во втором, с помощью функции JavaScript.

## <a name="examples"></a>Примеры

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Теперь используйте этот UDF в проекции запроса. При вызове их из запросов `udf.` необходимо квалифицировать UDF с приставкой.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Результаты:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Вы можете использовать квалификацию `udf.` UDF с приставкой внутри фильтра, как в следующем примере:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Результаты:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

По сути, UDF являются действительными скалярными выражениями, которые можно использовать как в проекциях, так и в фильтрах.

Чтобы расширить возможности UDF, посмотрите на другой пример с условной логикой:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Следующий пример упражнений UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Результаты:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Если свойства, упомянутые параметрами UDF, не доступны в значении JSON, параметр считается неопределенным и вызов UDF пропущен. Аналогичным образом, если результат UDF не определен, он не включен в результат.

Как следует из предыдущих примеров, UDF интегрируют силу языка JavaScript с API S'L. UDFs обеспечивают богатый программируемый интерфейс для выполнения сложной процедурной, условной логики с помощью встроенных возможностей выполнения JavaScript. API S'L предоставляет аргументы для UDF для каждого элемента источника на текущем этапе обработки положения WHERE или SELECT. Результат легко включен в общий конвейер выполнения. Таким образом, UDF являются отличными инструментами для сложной бизнес-логики как части запросов.

## <a name="next-steps"></a>Дальнейшие действия

- [Введение в Azure Космос DB](introduction.md)
- [Системные функции](sql-query-system-functions.md)
- [Статистические выражения](sql-query-aggregates.md)