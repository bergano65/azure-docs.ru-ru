---
title: ASIN на языке запросов Azure Cosmos DB
description: Сведения о функции "SQL System" ASIN в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5a64fc8d8f87d38f001bf2bc9dd581692c97fe64
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348496"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Возвращает угол в радианах, синус которого равен указанному числовому выражению. Также называется арксинусом.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере возвращается значение `ASIN` из-1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Результирующий набор:  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>Следующие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
