---
title: Датетиметотиместамп на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Датетиметотиместамп в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 11e0150089fd979cbf840d3b117c0d796ecc68f3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335727"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>Датетиметотиместамп (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  |mm|двузначное число минут (от 00 до 59)|
  |ss|двузначное число секунд (от 00 до 59)|
  |. fffffff|семь цифр в долях секунды|
  |Z|Обозначение времени в формате UTC (Универсальное координированное время)||
  
  Дополнительные сведения о формате ISO 8601 см. в разделе [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="return-types"></a>Типы возвращаемых данных

Возвращает числовое значение со знаком, равное текущему количеству миллисекунд, прошедших с момента выпуска UNIX, т. е. количество миллисекунд, истекших с 00:00:00 в четверг, 1 января 1970.

## <a name="remarks"></a>Комментарии

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
