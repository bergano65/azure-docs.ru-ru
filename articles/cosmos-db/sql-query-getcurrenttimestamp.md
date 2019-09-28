---
title: Жеткурренттиместамп на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Жеткурренттиместамп в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351008"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>Жеткурренттиместамп (Azure Cosmos DB)
 Возвращает число миллисекунд, прошедших с 00:00:00 четверг, 1 января 1970. 
  
## <a name="syntax"></a>Синтаксис
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое значение, текущее число миллисекунд, истекших с момента, прошедших с версии в 00:00:00 четверг, 1 января 1970.

## <a name="remarks"></a>Примечания

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

## <a name="next-steps"></a>Следующие шаги

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
