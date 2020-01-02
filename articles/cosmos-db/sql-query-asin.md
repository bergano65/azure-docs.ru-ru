---
title: ASIN на языке запросов Azure Cosmos DB
description: Узнайте, как функция арксинус (ASIN) SQL System в Azure Cosmos DB возвращает угол в радианах, синус которого является указанным числовым выражением.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e790eb5ae5eb780637b199a1a65dec2dd02d1bc
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871744"
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
  
  В следующем примере возвращается `ASIN`-1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Результирующий набор:  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
