---
title: Вложенные запросы SQL для Azure Cosmos DB
description: Дополнительные сведения о вложенных запросов SQL и их часто используется в Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: tisande
ms.openlocfilehash: 3ba547aea9034777fe76f3c911efd2648f6184fa
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514804"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Примеры вложенный запрос SQL для Azure Cosmos DB

Вложенный запрос является запросом, вложенных запросов. Вложенный запрос также называется внутренним запросом или внутренней операцией выбора, и инструкцию, содержащую вложенный запрос обычно вызывается внешнего запроса.

Существует два типа вложенных запросов:

* Коррелированное - Коррелированное вложенный запрос — это вложенный запрос, который ссылается на значения из внешнего запроса. Вложенный запрос вычисляется один раз для каждой строки, который обрабатывается с помощью внешнего запроса.

* Коррелированный Non - Некорреляционные вложенный запрос — это вложенный запрос, который не зависит от внешнего запроса, и он может выполняться на свой собственный без использования внешнего запроса.

> [!NOTE]
> Azure Cosmos DB поддерживает только коррелированные вложенные запросы.

## <a name="types-of-subqueries"></a>Типы вложенных запросов

Вложенные запросы можно более точно классифицировать на основе количества строк и столбцов, они возвращают. Существует три различных типа:
1.  **Таблица**. Возвращает несколько строк и несколько столбцов
2.  **Многозначный**: Возвращает несколько строк и один столбец
3.  **Скаляр**: Возвращает одну строку и один столбец

> [!NOTE]
> Azure Cosmos DB поддерживает многозначные и скалярные вложенные запросы

Azure Cosmos DB SQL-запросы всегда возвращает один столбец (значение простого или сложного документа). Таким образом в Azure Cosmos DB применимы только несколькими значениями и скалярные вложенные запросы выше. Вложенный запрос с несколькими значениями может использоваться только в предложении FROM как реляционного выражения, хотя скалярный вложенный запрос можно использовать как скалярное выражение в SELECT или предложении WHERE или как реляционных выражений в предложении FROM.


## <a name="multi-value-subqueries"></a>Вложенные запросы с несколькими значениями

Вложенные запросы с несколькими значениями возвращают набор документов, а также всегда используются в предложении FROM. Они используются для:

* Оптимизация выражения СОЕДИНЕНИЯ 
* Вычисление выражений дорогостоящие один раз и ссылки на несколько раз

### <a name="optimize-join-expressions"></a>Оптимизация выражения СОЕДИНЕНИЯ

Вложенные запросы с несколькими значениями можно оптимизировать выражения СОЕДИНЕНИЯ с помощью принудительной отправки предикаты, после каждого выражения select ко многим, а не после все кросс соединения в предложении WHERE.

Рассмотрим следующий запрос:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Для этого запроса индекс будет соответствовать любой документ, который содержит тег с именем «маленьким формулы», элемент питательный элемент со значением между 0 и 10 и элемент обслуживания со значение больше 1. Тем не менее здесь выражение ОБЪЕДИНЕНИЯ выполнит векторное произведение всех элементов, теги, nutrients и порций массивов для каждого соответствующего документа до применения фильтра. Предложение WHERE затем применяет кортежа фильтра предиката для каждого < c, t, n, s >. Например, если соответствующий документ содержит 10 элементов в каждом из трех массивов, он будет расширен до 1 x 10 x 10 x 10 (т. е. 1000) кортежей. Здесь использование вложенных запросов, может помочь фильтрации элементов массива, соединенных перед соединением с следующего выражения.

Этот запрос эквивалентен приведенному выше, но использует вложенные запросы:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

При условии, что только один элемент в массиве теги соответствует фильтру и пять элементов nutrients и порций массивы, выражения СОЕДИНЕНИЯ будут расширены для 1 x 1 x 5 x 5 = 25 элементы в отличие от 1000 элементов в первом запросе.

### <a name="evaluate-once-and-reference-many-times"></a>Вычислить один раз и справочник по много раз

Вложенные запросы может помочь оптимизировать запросы с дорогостоящим выражения, такие как определяемые пользователем функции (UDF) или сложную строку или арифметические выражения. Вложенный запрос, а также выражения СОЕДИНЕНИЯ можно использовать выражение один раз, но ссылаться на него много раз.

Следующий запрос выполняет определяемую пользователем Функцию GetMaxNutritionValue дважды:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Ниже приведен эквивалентный запрос, который только один раз выполняет определяемую пользователем Функцию.

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Учитывая поведение векторное произведение выражения СОЕДИНЕНИЯ, если выражение определяемая пользователем Функция может иметь неопределенное, следует убедиться, что выражение ОБЪЕДИНЕНИЯ всегда создает одну строку, возвращая объект из вложенного запроса, а не значение напрямую.
>

Ниже приведен похожий пример, который возвращает объект, а не значение.

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Этот подход не ограничивается определяемые пользователем функции, но вместо этого любому выражению потенциально затратным. Например мы может занять один и тот же подход с avg математические функции:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>Имитировать соединения с данными внешняя ссылка

Часто бывает необходимо ссылаться на статические данные, которые редко изменяется, например единиц измерения или коды стран. Для таких данных рекомендуется не его придется продублировать, для каждого документа. Предотвращение дублирования сохранит в хранилище и повышения производительности операций записи, оставив меньший размер документа. Вложенный запрос используется здесь для имитации семантику соединение inner с набором ссылочных данных.
Например рассмотрим следующий набор эталонных данных.

| **Единица измерения** | **Имя**            | **Множитель** | **Базовая единица** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1.00E-09       | Gram          |
| µg       | Microgram           | 1.00E-06       | Gram          |
| mg       | Миллиграмм           | 1.00E-03       | Gram          |
| ш        | Gram                | 1.00E + 00       | Gram          |
| кг       | Kilogram            | 1.00E + 03       | Gram          |
| MG       | Megagram            | 1.00E + 06       | Gram          |
| GG       | Gigagram            | 1.00E + 09       | Gram          |
| ДЖЕРСИ       | Nanojoule           | 1.00E-09       | Joule         |
| ΜJ       | Microjoule          | 1.00E-06       | Joule         |
| mJ       | Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E + 00       | Joule         |
| kJ       | Kilojoule           | 1.00E + 03       | Joule         |
| MJ       | Megajoule           | 1.00E + 06       | Joule         |
| GJ       | Gigajoule           | 1.00E + 09       | Joule         |
| Клиентская лицензия      | Калория             | 1.00E + 00       | калория       |
| kcal     | Калория             | 1.00E + 03       | калория       |
| IU       | Международные единицы |                |               |


Следующий запрос имитирует объединение с этими данными, что мы добавить имя блока выходных данных:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Скалярные вложенные запросы

Выражение скалярного вложенного запроса — это вложенный запрос, результатом которого является единственное значение. Выражение скалярного вложенного запроса значение значение проекции (предложение SELECT) вложенного запроса.  Выражение скалярного вложенного запроса можно использовать во многих местах скалярное выражение является допустимым. Например скалярный вложенный запрос можно использовать в любое выражение в обоих SELECT и предложениях WHERE.
Тем не менее с помощью скалярный вложенный запрос не всегда помогает оптимизировать. Например передачи скалярный вложенный запрос в качестве аргумента в системе или определяемые пользователем функции не дает никакой пользы в потребление ЕЗ или задержки.

Скалярные вложенные запросы можно более точно классифицировать как:
* Простое выражение скалярные вложенные запросы
* Агрегатные скалярные вложенные запросы

### <a name="simple-expression-scalar-subqueries"></a>Простое выражение скалярные вложенные запросы

Простое выражение скалярного вложенного запроса является коррелированный вложенный запрос с предложением SELECT, которая содержит любые статистические выражения. Эти вложенные запросы обеспечивают не преимущества оптимизации, так как компилятор преобразует их в один большего размера простое выражение. Отсутствует связанный контекст между внутренней и внешней запрос.

Рассмотрим несколько примеров.

**Пример 1**

```sql
SELECT 1 AS a, 2 AS b
```

Этот запрос можно переписать, используя простое выражение скалярного вложенного запроса для:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Этот вывод оба запроса:

```json
[
  { "a": 1, "b": 2 }
]
```

**Пример 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Этот запрос можно переписать, используя простое выражение скалярного вложенного запроса для:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Выходные данные запроса:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Пример 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Этот запрос можно переписать, используя простое выражение скалярного вложенного запроса для:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Выходные данные запроса:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

## <a name="aggregate-scalar-subqueries"></a>Агрегатные скалярные вложенные запросы

Агрегатные скалярный вложенный запрос — это вложенный запрос, который содержит агрегатную функцию в проекции или фильтра, результатом которого является единственное значение.

**Пример 1**.

Вот вложенный запрос с выражением одной агрегатной функции в его проекции:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Выходные данные запроса:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Пример 2**

Вложенный запрос с несколькими выражениями агрегатной функции:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Выходные данные запроса:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Пример 3**

Запрос с помощью агрегатных вложенный запрос в проекции и фильтрации:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Выходные данные запроса:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Более оптимальный способ написания этого запроса является соединение по вложенный запрос и ссылки на вложенный запрос псевдоним в обоих SELECT и предложения WHERE. Этот запрос является более эффективным, так как мы должна выполняться только в инструкцию соединения, а не в проекции и фильтрации вложенный запрос.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

### <a name="exists-expression"></a>Выражение EXISTS

Azure Cosmos DB поддерживает выражения EXISTS. Это статистические скалярный вложенный запрос встроенных в Azure Cosmos DB SQL API. EXISTS-это логическое выражение, которое принимает выражения вложенного запроса и возвращает значение true, если вложенный запрос возвращает все строки; в противном случае возвращается значение false.
Поскольку Azure Cosmos DB SQL API не делает различия между логических выражений и скалярных выражений, EXISTS можно использовать в обоих SELECT и предложения WHERE. Это, в отличие от T-SQL, где логическое выражение (например EXISTS, BETWEEN и IN) будет ограничен фильтр.

Если вложенный запрос EXISTS возвращает одно значение, которое не определено, EXISTS будет вычислять по false. Например рассмотрим следующий запрос, который принимает значение false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Тем не менее если пропущено ключевое слово значения во вложенном запросе выше затем запрос принимает значение true:
```sql
SELECT EXISTS (SELECT undefined) 
```

Вложенный запрос включит в себя список значений в списке выбора, в объекте. Если указанный список не имеет значений, вложенный запрос возвращает одно значение "{}" которого было определено и таким образом СУЩЕСТВУЕТ имеет значение true.

### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>Пример: Переписывания ARRAY_CONTAINS и МЕСТУ EXISTS

Часто из ARRAY_CONTAINS используется для фильтрации документа от наличия элемента в массиве. В этом случае выполняется проверка того, содержит ли массив тегов элемент с именем orange.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Тот же запрос можно переписать использование EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Кроме того ARRAY_CONTAINS только возможность проверить, если значение равно к любому элементу в массиве. Если для свойства массива требуются более сложные фильтры, соединение не требуется.

Рассмотрим следующий запрос, что фильтры на основе единиц и nutritionValue свойства в массиве: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Для каждого из документов в коллекции перекрестное произведение выполняется с помощью его элементам массива. Эта операция СОЕДИНЕНИЯ делает возможным фильтру для свойств в массиве. Тем не менее потребление ЕЗ этот запрос будет значительной. Например, если 1000 документов в 100 элементов в каждом массиве, он будет расширен до 1 000 x 100 (т. е. 100 000) кортежей.

С помощью `EXISTS` может помочь избежать этого дорогостоящие перекрестного произведения:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

В этом случае мы фильтрации по элементам массива в пределах вложенного запроса EXISTS. Если элемент массива, соответствующие критерию фильтра, то мы проекта и `EXISTS` имеет значение true.

Можно также псевдоним EXISTS и ссылаться на него в проекции:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Выходные данные запроса:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

### <a name="array-expression"></a>Выражение МАССИВА

`ARRAY` Выражение используется для проецирования результатов запроса как массив. Это выражение может использоваться только в предложении SELECT запроса.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Выходные данные запроса:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Как и в других вложенных запросов, фильтров с `ARRAY` возможны выражение.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Выходные данные запроса:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Массив выражений также могут поступать после предложения FROM во вложенных запросах.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Выходные данные запроса:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Примеры запросов SQL](how-to-sql-query.md)
- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Данные документов модели](modeling-data.md)
