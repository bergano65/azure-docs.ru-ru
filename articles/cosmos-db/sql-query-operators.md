---
title: Операторы запросов SQL для Azure Cosmos DB
description: Сведения об операторах SQL, таких как равенство, сравнение и логические операторы, поддерживаемые Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870944"
---
# <a name="operators-in-azure-cosmos-db"></a>Операторы в Azure Cosmos DB

В этой статье подробно описаны различные операторы, поддерживаемые Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Операторы сравнения и равенства

Ниже приведена таблица, в которой собраны результаты сравнения равенства в SQL API между любыми двумя типами JSON.

| **Оператор** | **Неопределенное** | **Null** | **Логический** | **Число** | **Строка** | **Объект** | **Массив** |
|---|---|---|---|---|---|---|---|
| **Неопределенное** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Допустимо** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Логический** | Undefined | Undefined | **Допустимо** | Undefined | Undefined | Undefined | Undefined |
| **Число** | Undefined | Undefined | Undefined | **Допустимо** | Undefined | Undefined | Undefined |
| **Строка** | Undefined | Undefined | Undefined | Undefined | **Допустимо** | Undefined | Undefined |
| **Объект** | Undefined | Undefined | Undefined | Undefined | Undefined | **Допустимо** | Undefined |
| **Массив** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Допустимо** |

Для операторов сравнения, таких как `>`, `>=`, `!=`, `<`и `<=`, сравнение по типам или между двумя объектами или массивами дает `Undefined`.  

Если результат скалярного выражения `Undefined`, элемент не включается в результат, так как `Undefined` не равно `true`.

## <a name="logical-and-or-and-not-operators"></a>Логические операторы (AND, OR или NOT)

Логические операторы работают над значениями типа Boolean. В следующих таблицах показаны логические таблицы истинности для этих операторов:

**Оператор OR**

| Или | Да | Нет | Undefined |
| --- | --- | --- | --- |
| Да |Да |Да |Да |
| Нет |Да |Нет |Undefined |
| Undefined |Да |Undefined |Undefined |

**Оператор AND**

| И | Да | Нет | Undefined |
| --- | --- | --- | --- |
| Да |Да |Нет |Undefined |
| Нет |Нет |Нет |Нет |
| Undefined |Undefined |Нет |Undefined |

**Оператор NOT**

| НЕ |  |
| --- | --- |
| Да |Нет |
| Нет |Да |
| Undefined |Undefined |


## <a name="-operator"></a>* - оператор

Специальный оператор * проецирует весь элемент как есть. При его использовании должно быть единственное отображаемое поле. Запрос, например `SELECT * FROM Families f`, является допустимым, но `SELECT VALUE * FROM Families f` и `SELECT *, f.id FROM Families f` недопустимы.

## <a name="-and--operators"></a>? и?? операторы

Для создания условных выражений можно использовать операторы Ternary (?) и объединения (??), такие как языки программирования, такие C# как и JavaScript. 

Вы можете использовать параметр? оператор для создания новых свойств JSON в режиме реального времени. Например, следующий запрос классифицирует уровни категории на `elementary` или `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Вы также можете вкладывать вызовы в? , как в следующем запросе: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Как и в случае с другими операторами запроса,? Оператор исключает элементы, если отсутствуют свойства, на которые имеются ссылки, или если сравниваемые типы различаются.

Используйте параметр?? оператор для эффективной проверки свойства в элементе при запросе частично структурированного или смешанного типа данных. Например, следующий запрос возвращает `lastName` если он есть, или `surname`, если `lastName` отсутствует.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Дальнейшие действия

- [Примеры .NET для Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Ключевые слова](sql-query-keywords.md)
- [Предложение SELECT](sql-query-select.md)
