---
title: GetCurrentDateTime на языке запросов Azure Cosmos DB
description: Узнайте о функции системы S'L GetCurrentDate time в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303908"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Возвращает текущую дату и время UTC (coordinated Universal Time) в виде строки ISO 8601.
  
## <a name="syntax"></a>Синтаксис
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает текущую дату utC и время iSO 8601 строки значение в формате, `YYYY-MM-DDThh:mm:ss.fffffffZ` где:
  
  |||
  |-|-|
  |YYYY|четырехзначный год|
  |ММ|двузначный месяц (01 январь и т.д.)|
  |DD|двузначный день месяца (01-31)|
  |T|знак для элементов начала времени|
  |hh|двузначный час (00-23)|
  |ММ|двузначные минуты (00-59)|
  |сс|двузначные секунды (00-59)|
  |.fffffff|семизначные дробные секунды|
  |Z|UTC (Кооркоорорное универсальное время) обозначение||
  
  Более подробную информацию о формате ISO 8601 можно оформить [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Remarks

  GetCurrentDateTime () является недетерминированной функцией. 
  
  Возвращается результат UTC.

  Точность составляет 7 цифр, с точностью 100 наносекунд.

## <a name="examples"></a>Примеры
  
  В следующем примере показано, как получить текущее время UTC Date Time с помощью встроенной функции GetCurrentDateTime() .
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Вот пример набора результатов.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
