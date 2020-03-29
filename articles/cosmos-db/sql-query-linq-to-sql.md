---
title: Перевод НА ЛИНЗ на перевод в Azure Cosmos DB
description: Изучите поддерживаемые операторами LIN's и то, как запросы LIN's отображаются в запросах СЗЛ в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441253"
---
# <a name="linq-to-sql-translation"></a>Трансляция из LINQ в SQL

Поставщик запросов Azure Cosmos DB выполняет наиболее эффективные усилия, отображая из запроса LIN's в запрос Cosmos DB S'L. Следующее описание предполагает базовое знакомство с LIN'.

Система типа поставщика запросов поддерживает только примитивные типы JSON: числовые, boolean, строки и нулевые.

Поставщик запросов поддерживает следующие скалярные выражения:

- Постоянные значения, включая постоянные значения примитивных типов данных во время оценки запроса.
  
- Выражения индекса свойств/массивов, относяющиеся к свойству объекта или элемента массива. Пример:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Арифметические выражения, в том числе общие арифметические выражения на численных и булейских ценностях. Для полного списка можно ознакомиться с [спецификацией Azure Cosmos DB S'L.](https://go.microsoft.com/fwlink/p/?LinkID=510612)
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Выражения сравнения строк, которые включают сравнение значения строки с некоторым постоянным значением строки.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Выражения создания объектов/массивов, которые возвращают объект типа сложного значения или анонимного типа, или массив таких объектов. Вы можете вложить эти значения.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Поддерживаемые операторы ЛИНЗ

Поставщик LIN', входящий в состав S'L .NET SDK, поддерживает следующих операторов:

- **Выберите**: Проекции переводят на S'L SELECT, включая строительство объекта.
- **Где**: Фильтры переводят на S'L WHERE, и поддерживают перевод между `&&`, `||`и `!` операторами S'L
- **SelectMany**: позволяет выполнять очистку массивов в предложение SQL JOIN. Используйте для цепных или гнездовых выражений для фильтрации элементов массива.
- **OrderBy** и **OrderByDescending**: Перевод на ORDER BY с ASC или DESC.
- Операторы для агрегирования **Count**, **Sum**, **Min**, **Max**, **Average** и их асинхронные эквиваленты **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** и **AverageAsync**.
- **CompareTo**: выполняет преобразование в сравнение диапазонов. Обычно используется для строк, так как они не сопоставимы в .NET.
- **Пропустить** и **взять**: Переводится на S'L OFFSET и LIMIT для ограничения результатов запроса и выполнения pagination.
- **Математические функции**: `Abs`Поддерживает `Acos` `Asin`перевод `Atan` `Ceiling`с `Cos` `Exp`.NET , , `Sign` `Sin`, `Sqrt` `Tan` `Truncate` `Floor` `Log` `Log10` `Pow` `Round`, , , , , , , , , , , , , , и в равной s'L встроенных функций.
- **Функции строки**: `Concat`Поддерживает `Contains` `Count`перевод `EndsWith``IndexOf`с `Replace` `Reverse`.NET `ToLower`, `ToUpper` `TrimEnd`, `TrimStart` , `StartsWith`, , `SubString`, , , , , , , и в эквивалентные встроенные функции S'L.
- **Функции array:** Поддерживает `Concat`перевод `Contains`с `Count` .NET , и к эквивалентным встроенным функциям S'L.
- **Функции геопространственного расширения:** поддерживает перевод `Distance`от `IsValid` `IsValidDetailed`методов `Within` заглушки, и к эквивалентным встроенным функциям S'L.
- **Функция расширения функции функциональности пользователя:** поддерживает `UserDefinedFunctionProvider.Invoke` перевод с метода заглушки на соответствующую функцию, определяемую пользователем.
- **Разное**: Поддерживает `Coalesce` перевод и условных операторов. Можно `Contains` переводить на строку CONTAINS, ARRAY_CONTAINS или S'L IN, в зависимости от контекста.

## <a name="examples"></a>Примеры

Следующие примеры иллюстрируют, как некоторые из стандартных операторов запросов LIN's переводятся на запросы Cosmos DB.

### <a name="select-operator"></a>Выбрать оператора

Синтаксис: `input.Select(x => f(x))`, где `f` — скалярное выражение.

**Выберите оператора, пример 1:**

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Выберите оператора, пример 2:** 

- **Лямбда-выражение LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Выберите оператора, пример 3:**

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

**Где оператор, на примере 1:**

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
  
**Где оператор, на примере 2:**

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

Можно составить состав предыдущих операторов для формирования более мощных запросов. Так как Cosmos DB поддерживает вложенные контейнеры, вы можете сконкатировать или приложить композицию.

### <a name="concatenation"></a>Объединение

Синтаксис: `input(.|.SelectMany())(.Select()|.Where())*`. Сонкатированный запрос может начинаться `SelectMany` с дополнительного `Select` запроса, за которым следуют несколько или `Where` операторы.

**Конкатация, пример 1:**

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

**Конкатация, пример 2:**

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

**Конкатация, пример 3:**

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

**Конкатация, пример 4:**

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

Синтаксис, `input.SelectMany(x=>x.Q())` `Q` где `Select`находится, `SelectMany` `Where` или оператор.

Вложенный запрос применяет внутренний запрос к каждому элементу внешнего контейнера. Одной из важных особенностей является то, что внутренний запрос может относиться к полям элементов во внешнем контейнере, как самосоединение.

**Гнездо, пример 1:**

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

**Гнездо, пример 2:**

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

**Гнездо, пример 3:**

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
- [Данные документов модели](modeling-data.md)
