---
title: Определяемые пользователем функции (UDF) в Azure Cosmos DB
description: Дополнительные сведения об определяемых пользователем функций в Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342770"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Определяемые пользователем функции (UDF) в Azure Cosmos DB

SQL API поддерживает определяемые пользователем функции (UDF). С определяемые пользователем скалярные функции вы можете передать в ноль или несколько аргументов и возвращают результат один аргумент. API проверяет каждый аргумент допустимым значениям JSON.  

API расширяет синтаксис SQL для поддержки настраиваемой логики приложения с помощью определяемых пользователем функций. Регистрация определяемых пользователем функций с помощью SQL API и ссылаться на них в SQL-запросов. На самом деле пользовательские функции специально предназначены для вызовов из запросов. Как следствие определяемых пользователем функций нет доступа к объекту контекста, как и других типов JavaScript, таких как хранимые процедуры и триггеры. Запросов доступны только для чтения и могут запускаться на первичной или вторичной реплики. Определяемые пользователем функции, в отличие от других типов JavaScript, предназначены для работы на вторичных репликах.

В следующем примере регистрируется определяемой пользователем функции в разделе контейнер элементов в базе данных Cosmos DB. В примере создается определяемая пользователем Функция с именем `REGEX_MATCH`. Он принимает два строковых значения JSON, `input` и `pattern`, и проверяет, предварительно сравниваются шаблоном, указанным во втором с помощью JavaScript `string.match()` функции.

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

Теперь можно Используйте эту определяемую пользователем Функцию в проекции запроса. Определяемые пользователем функции необходимо уточнить дополнять префиксом `udf.` при вызове их в запросах.

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

Можно использовать определяемую пользователем Функцию, дополнены `udf.` префикс в фильтре, как показано в следующем примере:

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

По сути определяемые пользователем функции являются корректными скалярными выражениями, которые можно использовать в проекциях и фильтрах.

Чтобы расширить возможности определяемых пользователем функций, рассмотрим другой пример с условной логикой:

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

Следующий пример выполняет определяемую пользователем Функцию:

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

Если свойства ссылались на по определяемой пользователем функции недоступны параметры в значения JSON, параметр рассматривается как не указано, и вызов определяемой пользователем функции пропускается. Аналогично Если результат определяемой пользователем функции не определен, он не включается в результат.

Как показано в предыдущих примерах, определяемые пользователем функции объединяют мощь языка JavaScript с помощью SQL API. Определяемые пользователем функции предоставляют богатый программируемый интерфейс для реализации сложной процедурной, условной логики с помощью встроенных возможностей исполнения JavaScript. SQL API на передает аргументы в определяемые пользователем функции для каждого исходного элемента текущего WHERE или SELECT этап обработки. Результат оно встраивается в общую выполнения конвейера. Таким образом определяемые пользователем функции являются отличным средством для реализации сложной бизнес-логики в результате запроса.

## <a name="next-steps"></a>Дальнейшие действия

- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
- [Системные функции](sql-query-system-functions.md)
- [статистические выражения](sql-query-aggregates.md)