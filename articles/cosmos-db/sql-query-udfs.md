---
title: Определяемые пользователем функции (UDF) в Azure Cosmos DB
description: Дополнительные сведения о функциях, определяемых пользователем, в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81011129"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Определяемые пользователем функции (UDF) в Azure Cosmos DB

API SQL обеспечивает поддержку определяемых пользователем функций (UDF). При использовании скалярных определяемых пользователем функций можно передать ноль или несколько аргументов и вернуть результат одного аргумента. API проверяет каждый аргумент на наличие допустимых значений JSON.  

## <a name="udf-use-cases"></a>Варианты использования UDF

API расширяет синтаксис SQL для поддержки пользовательской логики приложения с помощью пользовательских функций. Пользовательские функции можно зарегистрировать с помощью API SQL и ссылаться на них в SQL-запросах. В отличие от хранимых процедур и триггеров, UDF доступны только для чтения.

С помощью определяемых пользователем функций можно расширить язык запросов Azure Cosmos DB. UDF — это отличный способ выразить сложную бизнес-логику в проекции запроса.

Однако рекомендуется избегать использования UDF в следующих случаях:

- Эквивалентная [системная функция](sql-query-system-functions.md) уже существует в Azure Cosmos DB. Системные функции всегда будут использовать меньше единиц запросов, чем эквивалентная функция UDF.
- UDF является единственным фильтром в `WHERE` предложении запроса. Функция UDF не использует индекс, поэтому для оценки UDF потребуется загрузка документов. Объединение дополнительных предикатов фильтра, использующих индекс, в сочетании с определяемой пользователем функцией `WHERE` в предложении сокращает количество документов, ОБРАБАТЫВАЕМых определяемой пользователем функцией.

Если необходимо использовать одну и ту же UDF несколько раз в запросе, следует ссылаться на определяемую пользователем функцию во вложенном [запросе](sql-query-subquery.md#evaluate-once-and-reference-many-times), что позволяет использовать выражение объединения для однократного вычисления ОПРЕДЕЛЯЕМой пользователем функции, но ссылаться на нее много раз.

## <a name="examples"></a>Примеры

В следующем примере определяемая пользователем функция регистрируется в контейнере элементов в базе данных Cosmos. В примере создается определяемая пользователем функция `REGEX_MATCH`с именем. Он принимает два строковых значения JSON, `input` и `pattern`, и проверяет, соответствует ли первая шаблону, указанному во второй `string.match()` функции с помощью JavaScript.

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

Вы можете использовать определяемую пользователем функцию с `udf.` префиксом в фильтре, как показано в следующем примере:

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

Если свойства, на которые ссылаются параметры UDF, недоступны в значении JSON, параметр считается неопределенным и вызов UDF пропускается. Аналогично, если результат UDF не определен, он не включается в результат.

Как показано в предыдущих примерах, UDF интегрирует возможности языка JavaScript с API SQL. Определяемые пользователем функции предоставляют богатый программируемый интерфейс для выполнения сложной процедурной логики с помощью встроенных возможностей среды выполнения JavaScript. API SQL предоставляет аргументы для определяемых пользователем функций для каждого исходного элемента на этапе обработки текущего предложения WHERE или SELECT. Результат легко встроить в общий конвейер выполнения. В целом, UDF — это отличные средства для выполнения сложной бизнес-логики в рамках запросов.

## <a name="next-steps"></a>Дальнейшие шаги

- [Знакомство с Azure Cosmos DB](introduction.md)
- [Системные функции](sql-query-system-functions.md)
- [Статистические выражения](sql-query-aggregates.md)