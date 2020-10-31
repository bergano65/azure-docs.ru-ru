---
title: ГРАДУСы на языке запросов Azure Cosmos DB
description: Сведения о функции SQL System в ГРАДУСАХ в Azure Cosmos DB, чтобы получить соответствующий угол в градусах для угла, указанного в радианах
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 991d83a329603342975a8186fe704afc53813e08
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095770"
---
# <a name="degrees-azure-cosmos-db"></a>ГРАДУСы (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает соответствующее значение угла в градусах для угла, указанного в радианах.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример возвращает число градусов в угле, равном PI/2 радиан.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Результирующий набор:  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
