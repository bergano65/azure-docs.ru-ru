---
title: ATAN на языке запросов Azure Cosmos DB
description: Сведения о том, как функция ATAN Azure Cosmos DB в языке SQL в радианах Возвращает угол, тангенс которого является указанным числовым выражением.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a85967e97668af44c1f896d431c887cba2afb03c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081728"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает угол в радианах, тангенс которого равен указанному числовому выражению. Эта функция арктангенсом.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере возвращается `ATAN` значение указанного значения.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Результирующий набор:  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
