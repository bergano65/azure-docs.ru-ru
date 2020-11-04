---
title: СЛЧИС на языке запросов Azure Cosmos DB
description: Сведения о системной функции SQL RAND в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fb3e310970fcc2146ee0d4b790a9744dcd566bad
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341660"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает сгенерированное случайным образом числовое значение из [0, 1).
 
## <a name="syntax"></a>Синтаксис
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Типы возвращаемых данных

  Возвращает числовое выражение.

## <a name="remarks"></a>Комментарии

  `RAND` является недетерминированной функцией. Повторяющиеся вызовы не `RAND` возвращают одинаковые результаты. Эта системная функция не будет использовать индекс.


## <a name="examples"></a>Примеры
  
  В следующем примере возвращается числовое значение, сформированное случайным образом.
  
```sql
SELECT RAND() AS rand 
```  
  
 Результирующий набор:  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Дальнейшие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
