---
title: Жеткурренттиместамп на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Жеткурренттиместамп в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6720b0e5d13f2baaaf063fef2244b0c1f1863571
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341932"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>Жеткурренттиместамп (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает число миллисекунд, прошедших с 00:00:00 четверг, 1 января 1970.
  
## <a name="syntax"></a>Синтаксис
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
Возвращает числовое значение со знаком, равное текущему количеству миллисекунд, прошедших с момента выпуска UNIX, т. е. количество миллисекунд, истекших с 00:00:00 в четверг, 1 января 1970.

## <a name="remarks"></a>Комментарии

Жеткурренттиместамп () является недетерминированной функцией. Возвращаемый результат — UTC (всемирное время).

Эта системная функция не будет использовать индекс.

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

## <a name="next-steps"></a>Дальнейшие шаги

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
