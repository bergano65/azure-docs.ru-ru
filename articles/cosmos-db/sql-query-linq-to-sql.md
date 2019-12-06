---
title: Преобразование LINQ to SQL в Azure Cosmos DB
description: Узнайте о поддерживаемых операторах LINQ и о том, как запросы LINQ сопоставляются с запросами SQL в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d8dd6392cf22852a10c1dc2600edcbc647f3c510
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871165"
---
# <a name="linq-to-sql-translation"></a>Трансляция из LINQ в SQL

Поставщик запросов Azure Cosmos DB выполняет наилучшее сопоставление запроса LINQ с запросом на Cosmos DB SQL. Следующее описание предполагает базовое знание LINQ.

Система типов поставщиков запросов поддерживает только типы-примитивы JSON: numeric, Boolean, String и NULL.

Поставщик запросов поддерживает следующие скалярные выражения:

- Постоянные значения, включая постоянные значения типов данных-примитивов во время оценки запроса.
  
- Выражения индекса свойства или массива, которые ссылаются на свойство объекта или элемента массива. Пример.
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Арифметические выражения, включая общие арифметические выражения для числовых и логических значений. Полный список см. в [спецификации Azure Cosmos DB SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Выражения сравнения строк, которые включают сравнение строкового значения с некоторым постоянным строковым значением.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Выражения создания объектов и массивов, возвращающие объект составного типа значения или анонимного типа или массив таких объектов. Эти значения можно вложить.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>Поддерживаемые операторы LINQ

Поставщик LINQ, входящий в состав пакета SDK для SQL .NET, поддерживает следующие операторы:

- **SELECT**: проекции, переводимые в SQL SELECT, включая создание объектов.
- **Где**: ФИЛЬТРУЕТ в SQL, а также поддерживает преобразование между `&&`, `||`и `!` ОПЕРАТОРам SQL.
- **SelectMany**: позволяет выполнять очистку массивов в предложение SQL JOIN. Используйте, чтобы связать или вложить выражения для фильтрации элементов массива.
- **OrderBy** и **OrderByDescending**: преобразование для упорядочивания с ASC или DESC.
- Операторы для агрегирования **Count**, **Sum**, **Min**, **Max**, **Average** и их асинхронные эквиваленты **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** и **AverageAsync**.
- **CompareTo**: выполняет преобразование в сравнение диапазонов. Обычно используется для строк, так как они не сравнимы в .NET.
- **Take**: переводит в начало SQL для ограничения результатов запроса.
- **Математические функции**: поддерживает преобразование из .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`и `Sign`эквивалентным встроенным функциям SQL.
- **Строковые функции**: поддерживает преобразование из .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`и `TrimStart` эквивалентным встроенным функциям SQL.
- **Функции массивов**: поддерживает преобразование из .NET `Concat`, `Contains`и `Count` к эквивалентным встроенным функциям SQL.
- **Функции геопространственных расширений**: поддерживает преобразование из методов-заглушек `Distance`, `IsValid`, `IsValidDetailed`и `Within` в эквивалентные встроенные функции SQL.
- **Определяемая пользователем функция расширения функции**: поддерживает преобразование из метода-заглушки `UserDefinedFunctionProvider.Invoke` в соответствующую определяемую пользователем функцию.
- **Разное**: поддерживает преобразование `Coalesce` и условных операторов. Может преобразовывать `Contains` в строку CONTAINS, ARRAY_CONTAINS или SQL в зависимости от контекста.

## <a name="examples"></a>Примеры

В следующих примерах показано, как некоторые стандартные операторы запросов LINQ преобразуются в Cosmos DB запросы.

### <a name="select-operator"></a>Оператор SELECT

Синтаксис: `input.Select(x => f(x))`, где `f` — скалярное выражение.

**Оператор SELECT, пример 1:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Оператор SELECT, пример 2:** 

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Оператор SELECT, пример 3:**

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

**Оператор WHERE, пример 1:**

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
  
**Оператор WHERE, пример 2:**

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

Вы можете составить предшествующие операторы для формирования более эффективных запросов. Поскольку Cosmos DB поддерживает вложенные контейнеры, можно объединить или вложить композицию.

### <a name="concatenation"></a>Объединение

Синтаксис: `input(.|.SelectMany())(.Select()|.Where())*`. Объединенный запрос может начинаться с необязательного `SelectMany` запроса, за которым следуют несколько операторов `Select` или `Where`.

**Объединение, пример 1:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
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

**Объединение, пример 3.**

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

Синтаксис `input.SelectMany(x=>x.Q())`, где `Q` является оператором `Select`, `SelectMany`или `Where`.

Вложенный запрос применяет внутренний запрос к каждому элементу внешнего контейнера. Одной из важных возможностей является то, что внутренний запрос может ссылаться на поля элементов во внешнем контейнере, например на самосоединение.

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

**Вложение, пример 3:**

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

- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Данные документа модели](modeling-data.md)
