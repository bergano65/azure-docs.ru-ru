---
title: СЛЧИС на языке запросов Azure Cosmos DB
description: Сведения о системной функции SQL RAND в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ff098da778221868b0eddc17c426d2bf36eec0fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88794341"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Возвращает сгенерированное случайным образом числовое значение из [0, 1).
 
## <a name="syntax"></a>Синтаксис
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Типы возвращаемых данных

  Возвращает числовое выражение.

## <a name="remarks"></a>Remarks

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

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
