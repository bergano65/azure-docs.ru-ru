---
title: СЛЧИС на языке запросов Azure Cosmos DB
description: Сведения о системной функции SQL RAND в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302225"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Возвращает сгенерированное случайным образом числовое значение из [0, 1).
 
## <a name="syntax"></a>Синтаксис
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Типы возвращаемых данных

  Возвращает числовое выражение.

## <a name="remarks"></a>Комментарии

  `RAND` является недетерминированной функцией. Повторяющиеся вызовы не `RAND` возвращают одинаковые результаты.

## <a name="examples"></a>Примеры
  
  В следующем примере возвращается числовое значение, сформированное случайным образом.
  
```sql
SELECT RAND() AS rand 
```  
  
 Результирующий набор:  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
