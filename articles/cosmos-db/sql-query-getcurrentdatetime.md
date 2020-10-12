---
title: Жеткуррентдатетиме на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Жеткуррентдатетиме в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ec0b8ccaceed4abe3dd2784463f507f3bc76d890
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606964"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>Жеткуррентдатетиме (Azure Cosmos DB)

Возвращает текущее значение даты и времени (в формате UTC) в виде строки ISO 8601.
  
## <a name="syntax"></a>Синтаксис
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое значение даты и времени в формате UTC 8601 в формате, `YYYY-MM-DDThh:mm:ss.fffffffZ` где:
  
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

Жеткуррентдатетиме () является недетерминированной функцией. Возвращаемый результат — UTC. Точность составляет 7 цифр с точностью 100 наносекунд.

Эта системная функция не будет использовать индекс.

## <a name="examples"></a>Примеры
  
В следующем примере показано, как получить текущую дату и время в формате UTC с помощью встроенной функции Жеткуррентдатетиме ().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Ниже приведен пример результирующего набора.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
