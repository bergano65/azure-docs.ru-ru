---
title: DateTimeFromParts на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System DateTimeFromParts в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aec567c642f8eb3cb421ef5a119fe58f3a5fe05a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262231"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)

Возвращает строковое значение DateTime, созданное на основе входных значений.
  
## <a name="syntax"></a>Синтаксис
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>Аргументы
  
*нумбереар* Целочисленное значение года в формате`YYYY`

*нумбермонс*  
   Целочисленное значение месяца в формате`MM`

*нумбердай*  
   Целочисленное значение для дня в формате`DD`

*нумберхаур* (необязательно) целочисленное значение для часа в формате`hh`

*нумберминуте* (необязательно) целочисленное значение для минуты в формате`mm`

*нумберсеконд* (необязательно) целочисленное значение для второй в формате`ss`

*нумбероффрактионсофсеконд* (необязательно) целочисленное значение для дробной части секунды в формате`.fffffff`

## <a name="return-types"></a>Типы возвращаемых данных

Возвращает строковое значение даты и времени ISO 8601 по ГРИНВИЧу в формате, `YYYY-MM-DDThh:mm:ss.fffffffZ` где:
  
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

## <a name="remarks"></a>Remarks

Если указанные целые числа будут создавать недопустимое значение DateTime, DateTimeFromParts возвратит `undefined` .

Если необязательный аргумент не указан, его значение будет равно 0.

## <a name="examples"></a>Примеры

Ниже приведен пример, включающий только необходимые аргументы для создания даты и времени:

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

Вот еще один пример, в котором для создания даты и времени используются некоторые необязательные аргументы:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

Вот еще один пример, в котором также используются все необязательные аргументы для создания даты и времени:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
