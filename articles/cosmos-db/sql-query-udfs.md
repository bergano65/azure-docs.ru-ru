---
title: Функции, определяемые пользователями (UDF) в Azure Cosmos DB
description: Узнайте о функциях, определяемых пользователями, в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011129"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Функции, определяемые пользователями (UDF) в Azure Cosmos DB

API с помощью S'L обеспечивает поддержку функций, определяемых пользователями (UDF). С scalar UDFs, вы можете пройти в ноль или много аргументов и вернуть один результат аргумента. API проверяет каждый аргумент на наличие юридических значений JSON.  

## <a name="udf-use-cases"></a>Случаи использования UDF

API расширяет синтаксис S'L для поддержки логики пользовательских приложений с помощью UDFs. Вы можете зарегистрировать UDF с помощью API и ссылаться на них в запросах S'L. В отличие от сохраненных процедур и триггеров, UDF читаются только для чтения.

Используя UDF, можно расширить язык запросов Azure Cosmos DB. UDF - отличный способ выразить сложную бизнес-логику в проекции запроса.

Тем не менее, мы рекомендуем избегать UDFs, когда:

- Эквивалентная [функция системы](sql-query-system-functions.md) уже существует в Azure Cosmos DB. Системные функции всегда будут использовать меньше RU, чем эквивалентные UDF.
- UDF является единственным фильтром `WHERE` в пункте вашего запроса. UDF не используют индекс, поэтому для оценки UDF потребуются погрузочные документы. Объединение дополнительных предикатов фильтра, которые используют индекс, `WHERE` в сочетании с UDF, в оговорке позволит сократить количество документов, обрабатываемых UDF.

Если вы должны использовать один и тот же UDF несколько раз в запросе, вы должны ссылаться на UDF в [подзака,](sql-query-subquery.md#evaluate-once-and-reference-many-times)что позволяет использовать выражение JOIN для оценки UDF один раз, но ссылка на него много раз.

## <a name="examples"></a>Примеры

Следующий пример регистрирует UDF под контейнером элемента в базе данных Космос. Пример создает UDF, чье `REGEX_MATCH`имя . Он принимает два значения строки `input` `pattern`JSON и проверяет, соответствует ли первая модель, `string.match()` указанная во втором, с помощью функции JavaScript.

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

## <a name="next-steps"></a>Следующие шаги

- [Введение в Azure Космос DB](introduction.md)
- [Системные функции](sql-query-system-functions.md)
- [Статистические выражения](sql-query-aggregates.md)