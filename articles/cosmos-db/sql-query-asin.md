---
title: ASIN на языке запросов Azure Cosmos DB
description: Узнайте, как функция арксинус (ASIN) SQL System в Azure Cosmos DB возвращает угол в радианах, синус которого является указанным числовым выражением.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302701"
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
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере возвращается значение, `ASIN` равное-1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Результирующий набор:  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
