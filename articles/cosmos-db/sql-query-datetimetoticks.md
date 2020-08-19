---
title: Датетиметотиккс на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Датетиметотиккс в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2e2c9e8f2bf0d4760bf030fb19a90737cdb54525
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605135"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>Датетиметотиккс (Azure Cosmos DB)

Преобразует указанный DateTime в такты. Один такт представляет 100 наносекунд или 1 10-миллион секунды. 

## <a name="syntax"></a>Синтаксис
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Аргументы
  
*DateTime*  
   Строковое значение даты и времени ISO 8601 в формате UTC `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Типы возвращаемых данных

Возвращает числовое значение со знаком, текущее число 100-наносекундных тактов, прошедшее с момента создания эпохи UNIX. Иными словами, Датетиметотиккс возвращает число тактов 100-наносекундных импульсов, истекших с 00:00:00 четверг, 1 января 1970.

## <a name="remarks"></a>Комментарии

Датетимедатетиметотиккс возвращает, `undefined` Если дата и время не являются допустимыми ISO 8601 DateTime

Эта системная функция не будет использовать индекс.

## <a name="examples"></a>Примеры

Ниже приведен пример, возвращающий число тактов:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Ниже приведен пример, который возвращает количество тактов без указания числа долей секунды:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
