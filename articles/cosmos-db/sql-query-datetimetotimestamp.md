---
title: Датетиметотиместамп на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Датетиметотиместамп в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 673e6a8cb86986ff24e3ee55589d13e8a9e4fdc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88608828"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>Датетиметотиместамп (Azure Cosmos DB)

Преобразует указанный DateTime в отметку времени.
  
## <a name="syntax"></a>Синтаксис
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>Аргументы

*DateTime*  
   Значение даты и времени в формате UTC 8601 строка в формате, `YYYY-MM-DDThh:mm:ss.fffffffZ` где:
  
  |Формат|Описание|
  |-|-|
  |YYYY|год из четырех цифр|
  |ММ|месяц (2 цифры) (01 = Январь и т. д.)|
  |DD|четырехзначный день месяца (от 01 до 31)|
  |T|обозначает начало элементов времени|
  |hh|час с двумя цифрами (от 00 до 23)|
  |ММ|двузначное число минут (от 00 до 59)|
  |сс|двузначное число секунд (от 00 до 59)|
  |. fffffff|семь цифр в долях секунды|
  |Z|Обозначение времени в формате UTC (Универсальное координированное время)||
  
  Дополнительные сведения о формате ISO 8601 см. в разделе [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="return-types"></a>Типы возвращаемых данных

Возвращает числовое значение со знаком, равное текущему количеству миллисекунд, прошедших с момента выпуска UNIX, т. е. количество миллисекунд, истекших с 00:00:00 в четверг, 1 января 1970.

## <a name="remarks"></a>Remarks

Датетиметотиместамп возвращает, `undefined` Если указано недопустимое значение DateTime

## <a name="examples"></a>Примеры
  
В следующем примере Дата и время преобразуются в отметку времени:

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

Еще один пример:

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>Дальнейшие шаги

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
