---
title: SIN на языке запросов Azure Cosmos DB
description: Сведения о системной функции SQL SIN в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9f4c7933bed9d51e33ada21d03b9ab8447110e32
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082476"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает тригонометрический синус заданного угла в радианах для указанного выражения.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере вычисляется `SIN` указанный угол.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Результирующий набор:  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
