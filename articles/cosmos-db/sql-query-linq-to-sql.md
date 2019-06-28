---
title: LINQ для преобразования SQL в Azure Cosmos DB
description: Сопоставление запросов LINQ с запросами Azure Cosmos DB SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 057614da8fd29e1208c2788049c5d6d1a985eed5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342487"
---
# <a name="linq-to-sql-translation"></a>Трансляция из LINQ в SQL

Поставщик запросов Azure Cosmos DB выполняет сопоставление наиболее усилий из запроса LINQ в Cosmos DB SQL-запроса. Со следующим описанием предполагается Знакомство с LINQ.

Система типов поставщик запросов поддерживает только примитивные типы JSON: числовые, логические значения, строка и null.

Поставщик запросов поддерживает следующие скалярные выражения:

- Константные значения, включая постоянные значения примитивных типов данных во время оценки запроса.
  
- Выражения индекса или массив, которые относятся к свойствам объекта или элемента массива. Пример:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Арифметические выражения, включая общие арифметические выражения на основе численных и логических значений. Полный список см. в разделе [спецификации Azure Cosmos DB SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Выражения сравнения строки, которые включают сравнение строкового значения к некоторым постоянным строковым значением.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Выражения создания объекта или массива, которые возвращают объект комбинированного типа или анонимного типа, или массив таких объектов. Эти значения можно вкладывать.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>Поддерживаемых операторов LINQ

Поставщик LINQ, включенный в состав пакета SDK для .NET SQL поддерживает следующие операторы:

- **Select**. Проекции, преобразуются SQL SELECT, включая создание объектов.
- **Where**. Фильтры преобразуются в SQL WHERE и поддерживают преобразование `&&`, `||`, и `!` в операторы SQL.
- **SelectMany**. Позволяет выполнять очистку массивов в предложение SQL JOIN. Используйте для цепочки или создания вложенных выражений для фильтрации по элементам массива.
- **OrderBy** и **OrderByDescending**: Перевод для предложения ORDER BY с ASC или DESC.
- Операторы для агрегирования **Count**, **Sum**, **Min**, **Max**, **Average** и их асинхронные эквиваленты **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** и **AverageAsync**.
- **CompareTo**. Выполняет преобразование в сравнение диапазонов. Обычно используется для строк, так как их нельзя сравнивать в .NET.
- **Take**. Выполняет преобразование в SQL TOP для ограничения результатов запроса.
- **Математические функции**: Поддерживает преобразование из .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, и `Truncate` в эквивалентные встроенные функции SQL.
- **Строковые функции**: Поддерживает преобразование из .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, и `TrimStart` в эквивалентные встроенные функции SQL.
- **Функции массивов**: Поддерживает преобразование из .NET `Concat`, `Contains`, и `Count` в эквивалентные встроенные функции SQL.
- **Функции расширения геопространственных**: Поддерживает преобразование из методы-заглушки `Distance`, `IsValid`, `IsValidDetailed`, и `Within` в эквивалентные встроенные функции SQL.
- **Определяемая пользователем функция расширение функции**: Поддерживает преобразование из метода-заглушки `UserDefinedFunctionProvider.Invoke` для соответствующего определяемой пользователем функции.
- **Miscellaneous**. Поддерживает преобразование операторов `Coalesce` и условные операторы. Можно легко преобразовать `Contains` в строку CONTAINS, ARRAY_CONTAINS или SQL IN в зависимости от контекста.

## <a name="examples"></a>Примеры

В следующих примерах показано, как некоторые из стандартных операторов запросов LINQ преобразуются в запросы Cosmos DB.

### <a name="select-operator"></a>Выберите оператор

Синтаксис: `input.Select(x => f(x))`, где `f` — скалярное выражение.

**Выберите оператор, пример 1:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Выберите оператор, пример 2:** 

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Выберите оператор, пример 3:**

- **Лямбда-выражение LINQ**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>Оператор SelectMany

Синтаксис: `input.SelectMany(x => f(x))`, где `f` — скалярное выражение, возвращающее тип контейнера.

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Оператор Where

Синтаксис: `input.Where(x => f(x))`, где `f` — скалярное выражение, возвращающее логическое значение.

**Когда оператор, пример 1:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Когда оператор, пример 2:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Составные SQL-запросы

Можно составить перечисленные выше операторы, чтобы формировать более расширенные запросы. Так как Cosmos DB поддерживает вложенные контейнеры, можно объединить или вложить композиции.

### <a name="concatenation"></a>Объединение

Синтаксис: `input(.|.SelectMany())(.Select()|.Where())*`. Объединенный запрос может начинаться с необязательного `SelectMany` запроса, за которым идет несколько `Select` или `Where` операторы.

**Объединение, пример 1:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Объединение, пример 2:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Объединение, пример 3:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Объединение, пример 4:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Вложенные операторы

Синтаксис `input.SelectMany(x=>x.Q())` где `Q` — `Select`, `SelectMany`, или `Where` оператор.

Вложенный запрос применяет внутренний запрос к каждому элементу внешнего контейнера. Одной важной особенностью является то, что внутренний запрос может ссылаться на поля элементов внешнего контейнера, как самосоединение.

**Вложенность, пример 1:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Вложенность, пример 2:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Вложенность, пример 3:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>Дальнейшие действия

- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Данные документов модели](modeling-data.md)
