---
title: Определяемые пользователем функции (UDF) в Azure Cosmos DB
description: Дополнительные сведения о функциях, определяемых пользователем, в Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614330"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Определяемые пользователем функции (UDF) в Azure Cosmos DB

API SQL обеспечивает поддержку определяемых пользователем функций (UDF). При использовании скалярных определяемых пользователем функций можно передать ноль или несколько аргументов и вернуть результат одного аргумента. API проверяет каждый аргумент на наличие допустимых значений JSON.  

API расширяет синтаксис SQL для поддержки пользовательской логики приложения с помощью пользовательских функций. Пользовательские функции можно зарегистрировать с помощью API SQL и ссылаться на них в SQL-запросах. На самом деле пользовательские функции специально предназначены для вызовов из запросов. В качестве кообъекта UDF не имеет доступа к контекстному объекту, например к другим типам JavaScript, таким как хранимые процедуры и триггеры. Запросы доступны только для чтения и могут запускаться на первичных или вторичных репликах. Пользовательские функции, в отличие от других типов JavaScript, предназначены для запуска на вторичных репликах.

В следующем примере определяемая пользователем функция регистрируется в контейнере элементов в базе данных Cosmos. В примере создается определяемая пользователем функция `REGEX_MATCH`с именем. Он принимает два строковых значения JSON, `input` `pattern`и, и проверяет, соответствует ли первая шаблону, указанному во второй `string.match()` функции с помощью JavaScript.

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

Теперь используйте эту определяемую пользователем функцию в проекции запроса. Определяемые пользователем функции необходимо указывать с префиксом `udf.` , учитывающим регистр, при вызове их из запросов.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Получаются такие результаты:

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

Вы можете использовать определяемую пользователем функцию с `udf.` префиксом в фильтре, как показано в следующем примере:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Получаются такие результаты:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

По сути, UDF являются допустимыми скалярными выражениями, которые можно использовать как в проекциях, так и в фильтрах.

Чтобы расширить возможности пользовательских функций, просмотрите другой пример с условной логикой:

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

В следующем примере приведена функция UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Получаются такие результаты:

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

Если свойства, на которые ссылаются параметры UDF, недоступны в значении JSON, параметр считается неопределенным и вызов UDF пропускается. Аналогично, если результат UDF не определен, он не включается в результат.

Как показано в предыдущих примерах, UDF интегрирует возможности языка JavaScript с API SQL. Определяемые пользователем функции предоставляют богатый программируемый интерфейс для выполнения сложной процедурной логики с помощью встроенных возможностей среды выполнения JavaScript. API SQL предоставляет аргументы для определяемых пользователем функций для каждого исходного элемента на этапе обработки текущего предложения WHERE или SELECT. Результат легко встроить в общий конвейер выполнения. В целом, UDF — это отличные средства для выполнения сложной бизнес-логики в рамках запросов.

## <a name="next-steps"></a>Следующие шаги

- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Системные функции](sql-query-system-functions.md)
- [Статистические выражения](sql-query-aggregates.md)