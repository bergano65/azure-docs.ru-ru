---
title: Жеткурренттиккс на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Жеткурренттиккс в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 4841c374454dea9afdf0dbc4094311ded54cfbb6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098524"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>Жеткурренттиккс (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Возвращает число 100-наносекундных тактов, истекших с 00:00:00 четверг, 1 января 1970.
  
## <a name="syntax"></a>Синтаксис
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Типы возвращаемых данных

Возвращает числовое значение со знаком, текущее число 100-наносекундных тактов, прошедшее с момента создания эпохи UNIX. Иными словами, Жеткурренттиккс возвращает число импульсов 100 наносекунд, истекших с 00:00:00 четверг, 1 января 1970.

## <a name="remarks"></a>Комментарии

Жеткурренттиккс () является недетерминированной функцией. Возвращаемый результат — UTC (всемирное время).

Эта системная функция не будет использовать индекс.

## <a name="examples"></a>Примеры

Ниже приведен пример, который возвращает текущее время, измеряемое в тактах:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
