---
title: Подзапросы с S'L для Azure Cosmos DB
description: Узнайте о подзапросах S'L и их общих случаях использования и различных типах подзапросов в Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870570"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Примеры подсказок для Azure Cosmos DB

Выкорок — это запрос, вложенный в другой запрос. Выкорок также называется внутренним запросом или внутренним выбором. Заявление, содержащее подкато, обычно называется внешним запросом.

В этой статье описаны субзапросы s'L и их общие случаи использования в Azure Cosmos DB. Все выборочные запросы в этом документе могут быть запущены с набором данных о питании, который предварительно загружен на [игровой площадке Azure Cosmos DB.](https://www.documentdb.com/sql/demo)

## <a name="types-of-subqueries"></a>Типы подзапросов

Существует два основных типа подзапросов:

* **Коррелированный**: Подкажий, который ссылается на значения из внешнего запроса. Выкорок оценивается один раз для каждой строки, которую обрабатывает внешний запрос.
* **Не коррелирует**: подзаголовок, который не зависит от внешнего запроса. Он может работать самостоятельно, не полагаясь на внешний запрос.

> [!NOTE]
> Azure Cosmos DB поддерживает только коррелированные подзапросы.

Подзапросы могут быть классифицированы в зависимости от количества строк и столбцов, которые они возвращают. Существует три типа:
* **Таблица**: Возвращает несколько строк и несколько столбцов.
* **Многозначение**: Возвращает несколько строк и один столбец.
* **Scalar**: Возвращает одну строку и одну колонку.

Запросы в Azure Cosmos DB всегда возвращают один столбец (простое значение или сложный документ). Таким образом, в Azure Cosmos DB применимы только мультиценные и масштабированные подзапросы. Вы можете использовать многоценный подзаголовок только в оговорке FROM в качестве реляционного выражения. Вы можете использовать секральное подкато в качестве секларового выражения в пункте SELECT или WHERE, или как реляционное выражение в оговорке FROM.

## <a name="multi-value-subqueries"></a>Мультиценные субзапросы

Мультиценные субзапросы возвращают набор документов и всегда используются в пункте FROM. Они используются для:

* Оптимизация выражений JOIN. 
* Оценка дорогих выражений один раз и ссылки несколько раз.

## <a name="optimize-join-expressions"></a>Оптимизация выражений JOIN

Мульти-значение подзапросов может оптимизировать JOIN выражения, нажав предикаты после каждого выбора много выражения, а не после всех перекрестных соединений в пункте WHERE.

Обратите внимание на следующий запрос:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Для этого запроса индекс будет соответствовать любому документу с тегом с названием "младенческая формула". Это элемент питательного вещества со значением от 0 до 10, и сервий пункт с количеством больше, чем 1. Выражение JOIN здесь будет выполнять кросс-продукт всех элементов тегов, питательных веществ и сервитутов массивов для каждого соответствующего документа, прежде чем любой фильтр применяется. 

Положение WHERE будет применять предикат фильтра на каждом <c, t, n, s> tuple. Например, если в соответствующем документе было по 10 элементов в каждом из трех массивов, он расширится до 1 x 10 x 10 x 10 (т.е. 1000) в уходе. Использование подзапросов может помочь в фильтрации объединенных элементов массива, прежде чем присоединиться к следующему выражению.

Этот запрос эквивалентен предыдущему, но использует подзапросы:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Предположим, что только один элемент в массиве тегов соответствует фильтру, и есть пять элементов как для питательных веществ, так и для массивов порций. Выражения JOIN расширятся до 1 x 1 x x 5 x 5 х 25 элементов, в отличие от 1000 элементов в первом запросе.

## <a name="evaluate-once-and-reference-many-times"></a>Оцените один раз и ссылки много раз

Подзапросы могут помочь оптимизировать запросы с дорогими выражениями, такими как пользовательские функции (UDF), сложные строки или арифметические выражения. Вы можете использовать подкато вместе с выражением JOIN, чтобы оценить выражение один раз, но ссылаться на него много раз.

Следующий запрос выполняет UDF `GetMaxNutritionValue` дважды:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Вот эквивалентный запрос, который выполняет UDF только один раз:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Имейте в виду кросс-продукт поведения JOIN выражений. Если выражение UDF может оценить неопределенным, следует убедиться, что выражение JOIN всегда производит одну строку, вернув объект из подвоха, а не непосредственное значение.
>

Вот аналогичный пример, который возвращает объект, а не значение:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Этот подход не ограничивается ОДФ. Это относится к любому потенциально дорогому выражению. Например, можно воспользоваться тем же `avg`подходом с математической функцией:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Имитировать соединение с внешними справочными данными

Часто может потребоваться ссылка на статические данные, которые редко изменяются, такие как единицы измерения или коды стран. Для каждого документа лучше не дублировать такие данные. Избежание такого дублирования позволит сэкономить на хранении и повысить производительность записи, сохранив размер документа меньше. Вы можете использовать подкато, чтобы имитировать семантику внутреннего соединения с набором справочных данных.

Например, рассмотрим этот набор справочных данных:

| **Единица измерения** | **Название**            | **Множитель** | **Базовый блок** |
| -------- | ------------------- | -------------- | ------------- |
| Ng       | Нанограмма            | 1.00E-09       | Грамм          |
| Мкг       | Микрограмм           | 1.00E-06       | Грамм          |
| mg       | Миллиграмм           | 1.00E-03       | Грамм          |
| g        | Грамм                | 1.00E-00       | Грамм          |
| кг       | Килограмм            | 1.00E-03       | Грамм          |
| Мг       | Мегаграмма            | 1.00E-06       | Грамм          |
| Gg       | Гигаграмма            | 1.00E-09       | Грамм          |
| Nj       | Наноджоуль           | 1.00E-09       | Джоуль         |
| «J       | Микроджоуль          | 1.00E-06       | Джоуль         |
| Мдж       | Миллиджоул          | 1.00E-03       | Джоуль         |
| J        | Джоуль               | 1.00E-00       | Джоуль         |
| Кдж       | Кдж           | 1.00E-03       | Джоуль         |
| Мдж       | Мегаджоуль           | 1.00E-06       | Джоуль         |
| Гдж       | Гигаджоул           | 1.00E-09       | Джоуль         |
| cal      | Калорий             | 1.00E-00       | Калорий       |
| Ккал     | Калорий             | 1.00E-03       | Калорий       |
| Ме       | Международные подразделения |                |               |


Следующий запрос имитирует присоединение к этим данным, так что вы добавляете имя единицы к выходу:

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

## <a name="scalar-subqueries"></a>скалярные вложенные запросы;

Секлярное субкаврисическое выражение — это подзака, которая оценивает сяокопию в одно значение. Значение скалярного выражающего выражения — значение проекции (оговорка SELECT) подзака.  Вы можете использовать секлярное выражаемые выражения во многих местах, где выражение scalar является действительным. Например, вы можете использовать масштабируемую субзапрос в любом выражении как в предложениях SELECT, так и where.

Однако использование масштабируемого subquery не всегда помогает оптимизировать его. Например, передача масштабируемого субзапроса в качестве аргумента в качестве аргумента в качестве аргумента в пользу системных или пользовательских функций не дает никаких преимуществ в потреблении или задержке единицы ресурсов (RU).

Подзапросы Scalar могут быть классифицированы как:
* Простое выражение скалярных субзапросов
* Агрегатные подзапросы масштабирования

## <a name="simple-expression-scalar-subqueries"></a>Простое выражение скалярных субзапросов

Простое секрарическое подкатое простое выражение — это коррелированное подъемное, содержащее положение SELECT, не содержащее агрегированных выражений. Эти подзапросы не дают преимуществ для оптимизации, поскольку компилятор преобразует их в одно более крупное простое выражение. Между внутренними и внешними запросами нет коррелирует контекст.

Рассмотрим несколько примеров.

**Пример 1**

```sql
SELECT 1 AS a, 2 AS b
```

Вы можете переписать этот запрос, используя простое выражение секалярной субзапроса, чтобы:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Оба запроса дают такую выходку:

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

Вы можете переписать этот запрос, используя простое выражение секалярной субзапроса, чтобы:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Выход запроса:

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

Вы можете переписать этот запрос, используя простое выражение секалярной субзапроса, чтобы:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Выход запроса:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Агрегатные подзапросы масштабирования

Совокупный секравл- представляет собой субзапрос, который имеет агрегированную функцию в проекции или фильтре, которая оценивается до одного значения.

**Пример 1.**

Вот подкато с единым выражением функции в проекции:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Выход запроса:

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

Вот подзаголовок с несколькими агрегированными выражениями функции:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Выход запроса:

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

Вот запрос с совокупным вырезом как в проекции, так и в фильтре:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Выход запроса:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Более оптимальным способом написания этого запроса является присоединение к подзапросу и ссылка на подкаженый псевдоним как в предложениях SELECT, так и в WHERE. Этот запрос является более эффективным, поскольку выполнить субзапрос только в выписке соединения, а не в проекции и фильтре.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>ВЫРАЖЕНИЕ EXISTS

Azure Cosmos DB поддерживает выражения EXISTS. Это совокупный масштабный подзачет, встроенный в API Azure Cosmos DB S'L. EXISTS является выражением Boolean, которое принимает выдавливное выражение и возвращается верно, если подкачка возвращает любые строки. В противном случае возвращается значение false.

Поскольку API Azure Cosmos DB S'L не проводит различия между выражениями Boolean и любыми другими секларами, можно использовать EXISTS как в предложениях SELECT, так и where. Это не похоже на T-S'L, где выражение Boolean (например, EXISTS, BETWEEN, и IN) ограничено фильтром.

Если subquery EXISTS возвращает одно значение, которое не определено, EXISTS будет оцениваться как ложное. Например, рассмотрим следующий запрос, который оценивается как ложный:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Если ключевое слово VALUE в предыдущем выдавлине опущено, запрос будет оцениваться достоверно:
```sql
SELECT EXISTS (SELECT undefined) 
```

Подзаголовок будет привязать список значений в выбранный список в объекте. Если выбранный список не имеет значений, подзапрос возвращает{}одно значение ' '. Это значение определено, поэтому EXISTS оценивает сядеек.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Пример: Переписывание ARRAY_CONTAINS и JOIN как EXISTS

Общим примером ARRAY_CONTAINS является фильтрация документа наличием элемента в массиве. В этом случае мы проверяем, содержит ли массив тегов элемент под названием "оранжевый".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Вы можете переписать тот же запрос, чтобы использовать EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Кроме того, ARRAY_CONTAINS можете проверить, равен ли значение любому элементу в массиве. Если вам нужны более сложные фильтры по свойствам массива, используйте JOIN.

Рассмотрим следующий запрос, который фильтрует `nutritionValue` на основе единиц и свойств в массиве: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Для каждого из документов, наиболее представленных в коллекции, выполняется кросс-продукт с элементами массива. Эта операция JOIN позволяет фильтровать свойства внутри массива. Тем не менее, потребление RU этого запроса будет значительным. Например, если в 1000 документах было по 100 элементов в каждом массиве, он расширится до 1000 х 100 (т.е. 100 000) в утилизах.

Использование EXISTS может помочь избежать этого дорогого кросс-продукта:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

В этом случае вы фильтруете элементы массива в подзапросе EXISTS. Если элемент массива соответствует фильтру, то вы проецируете его и EXISTS оценивает сядеек.

Вы также можете псевдоним EXISTS и ссылаться на него в проекции:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Выход запроса:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>Выражение ARRAY

Выражение ARRAY можно использовать для прогнозирования результатов запроса в виде массива. Это выражение можно использовать только в пункте запроса SELECT.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Выход запроса:

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

Как и в других подзапросах, возможны фильтры с выражением ARRAY.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Выход запроса:

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

Выражения массива также могут прийти после положения FROM в подзапросах.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Выход запроса:

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

- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Данные документов модели](modeling-data.md)
