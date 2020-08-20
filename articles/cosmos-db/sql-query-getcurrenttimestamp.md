---
title: Жеткурренттиместамп на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Жеткурренттиместамп в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c1e0a8d69edab0c01005268ee49c23625236f03a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606919"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>Жеткурренттиместамп (Azure Cosmos DB)

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

## <a name="next-steps"></a>Дальнейшие действия

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
