---
title: СЛЧИС на языке запросов Azure Cosmos DB
description: Сведения о системной функции SQL RAND в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4a0672000e630c9e06df84d9c2da5cb8b988c05a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349604"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Возвращает сгенерированное случайным образом числовое значение из [0, 1).
 
## <a name="syntax"></a>Синтаксис
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Возвращаемые типы

  Возвращает числовое выражение.

## <a name="remarks"></a>Примечания

  `RAND` является недетерминированной функцией. Повторяющиеся вызовы `RAND` не возвращают одинаковые результаты.

## <a name="examples"></a>Примеры
  
  В следующем примере возвращается числовое значение, сформированное случайным образом.
  
```sql
SELECT RAND() AS rand 
```  
  
 Результирующий набор:  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Следующие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
