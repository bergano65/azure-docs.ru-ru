---
title: Агрегированные функции в Azure Cosmos DB
description: Узнайте о синтаксисе агрегированных функций S'L, типах агрегированных функций, поддерживаемых Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464467"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Агрегированные функции в Azure Cosmos DB

Агрегатные функции выполняют расчет по набору значений в предложении `SELECT` и возвращают одно значение. Например, следующий запрос возвращает количество элементов в контейнере: `Families`

## <a name="examples"></a>Примеры

```sql
    SELECT COUNT(1)
    FROM Families f
```

Результаты:

```json
    [{
        "$1": 2
    }]
```

Вы также можете вернуть только масштабируемое значение агрегата с помощью ключевого слова VALUE. Например следующий запрос возвращает число значений как одно число:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Результаты:

```json
    [ 2 ]
```

Можно также комбинировать агрегации с фильтрами. Например, следующий запрос возвращает количество элементов с `WA`состоянием адреса.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Результаты:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Типы агрегированных функций

API S'L поддерживает следующие агрегированные функции. `SUM`и `AVG` работать на числовые `COUNT` `MIN`значения, `MAX` и , и работать на номера, строки, booleans, и nulls.

| Компонент | Описание |
|-------|-------------|
| COUNT | Возвращает число элементов в выражении. |
| SUM   | Возвращает сумму всех элементов в выражении. |
| MIN   | Возвращает минимальное значение выражения. |
| MAX   | Возвращает максимальное значение выражения. |
| AVG   | Возвращает среднее арифметическое значений в выражении. |

Можно также агрегировать результаты итерации массива.

> [!NOTE]
> В Data Explorer портала Azure запросы агрегирования могут агрегировать частичные результаты только на одной странице запроса. SDK производит единое совокупное значение на всех страницах. Для выполнения агрегационных запросов с помощью кода необходимо .NET SDK 1.12.0, .NET Core SDK 1.1.0 или Java SDK 1.9.5 или выше.

## <a name="remarks"></a>Remarks

Эти функции агрегированной системы выиграют от [индекса диапазона.](index-policy.md#includeexclude-strategy) Если вы ожидаете `COUNT` `SUM` `MIN`сделать, , , `MAX`, , или `AVG` на имущество, вы должны включить [соответствующий путь в политике индексации.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Дальнейшие действия

- [Введение в Azure Космос DB](introduction.md)
- [Системные функции](sql-query-system-functions.md)
- [Функции, определенные пользователем](sql-query-udfs.md)