---
title: ACOS на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System ACOS в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c9d29fba6b5dc55a98bf90cfafe0940d7bf9674
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348741"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Возвращает угол в радианах, косинус которого равен указанному числовому выражению; также называется арккосинусом.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере возвращается значение `ACOS` из-1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Результирующий набор:  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Следующие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
