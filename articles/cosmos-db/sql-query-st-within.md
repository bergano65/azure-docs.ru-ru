---
title: ST_WITHIN языка запросов Azure Cosmos DB
description: Сведения о ST_WITHIN системных функций SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e808f6f56041e6cdc577164140910af8d6c42731
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004404"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает логическое выражение, указывающее, находится ли объект геоjson (точка, многоугольник, многоугольный или LineString), указанный в первом аргументе, в пределах геоjson (Point, многоугольник, Polygon или LineString) во втором аргументе.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*spatial_expr*  
   — Это выражение геообъектной точки, многоугольника или объекта LineString.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое значение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как найти все документы семейства в пределах многоугольника с помощью `ST_WITHIN` .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Результирующий набор:  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция будет полезна из [геопространственных индексов](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Следующие шаги

- [Пространственные функции Azure Cosmos DB](sql-query-spatial-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
