---
title: Датетиметотиккс на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Датетиметотиккс в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a6fcff691ee5278ed0a9e1c132f8ecebbee9431c
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227245"
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

Возвращает положительное целое значение.

## <a name="remarks"></a>Remarks

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
