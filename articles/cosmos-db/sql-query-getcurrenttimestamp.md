---
title: Жеткурренттиместамп на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Жеткурренттиместамп в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9c35f83ce7a9a478f706e9ed560d884d9bf5e508
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261292"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>Жеткурренттиместамп (Azure Cosmos DB)

 Возвращает число миллисекунд, прошедших с 00:00:00 четверг, 1 января 1970.
  
## <a name="syntax"></a>Синтаксис
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое значение, текущее число миллисекунд, истекших с момента, прошедших с версии в 00:00:00 четверг, 1 января 1970.

## <a name="remarks"></a>Remarks

  Жеткурренттиместамп () является недетерминированной функцией.
  
  Возвращаемый результат — UTC (всемирное время).

## <a name="examples"></a>Примеры
  
  В следующем примере показано, как получить текущую метку времени с помощью встроенной функции Жеткурренттиместамп ().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Ниже приведен пример результирующего набора.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Дальнейшие действия

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
