---
title: COS на языке запросов Azure Cosmos DB
description: Сведения о том, как косинус (COS) SQL System в Azure Cosmos DB возвращает тригонометрический косинус указанного угла в радианах в указанном выражении
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78304027"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Возвращает тригонометрический косинус указанного угла в радианах в указанном выражении.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере вычисляется `COS` указанный угол.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Результирующий набор:  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
