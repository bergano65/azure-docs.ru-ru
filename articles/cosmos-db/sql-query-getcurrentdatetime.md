---
title: Жеткуррентдатетиме на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Жеткуррентдатетиме в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8a2c3dcd3c8ca6dc9d751e50a7862fe98e6de510
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351028"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>Жеткуррентдатетиме (Azure Cosmos DB)
 Возвращает текущее значение даты и времени (в формате UTC) в виде строки ISO 8601.
  
## <a name="syntax"></a>Синтаксис
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает текущее строковое значение даты и времени в формате UTC 8601 в формате `YYYY-MM-DDThh:mm:ss.sssZ`, где:
  
  |||
  |-|-|
  |YYYY|год из четырех цифр|
  |ММ|месяц (2 цифры) (01 = Январь и т. д.)|
  |DD|четырехзначный день месяца (от 01 до 31)|
  |н|обозначает начало элементов времени|
  |hh|час в две цифры (от 00 до 23)|
  |ММ|минуты в две цифры (от 00 до 59)|
  |НН|две цифры в секундах (от 00 до 59)|
  |. SSS|три цифры десятичных дробей секунды|
  |Z|Обозначение времени в формате UTC (Универсальное координированное время)||
  
  Дополнительные сведения о формате ISO 8601 см. в разделе [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Примечания

  Жеткуррентдатетиме () является недетерминированной функцией. 
  
  Возвращаемый результат — UTC.

## <a name="examples"></a>Примеры
  
  В следующем примере показано, как получить текущую дату и время в формате UTC с помощью встроенной функции Жеткуррентдатетиме ().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Ниже приведен пример результирующего набора.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

## <a name="next-steps"></a>Следующие шаги

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
