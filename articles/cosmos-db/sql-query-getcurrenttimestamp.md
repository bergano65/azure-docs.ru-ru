---
title: GetCurrentTimestamp на языке запросов Azure Cosmos DB
description: Узнайте о функции системы СЗЛ GetCurrentTimestamp в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351008"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Возвращает количество миллисекунд, прошедшее с 00:00 четверга, 1 января 1970 года. 
  
## <a name="syntax"></a>Синтаксис
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое значение, текущее количество миллисекунд, прошедшее со времен эпохи Unix, т.е. количество миллисекунд, прошедшее с 00:00 четверга, 1 января 1970 года.

## <a name="remarks"></a>Remarks

  GetCurrentTimestamp () является недетерминированной функцией.
  
  Возвращается результат UTC (Кооркоординированное универсальное время).

## <a name="examples"></a>Примеры
  
  Ниже приводится следующий пример, как получить текущую метку времени с помощью встроенной функции GetCurrentTimestamp()
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Вот пример набора результатов.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Дальнейшие действия

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
