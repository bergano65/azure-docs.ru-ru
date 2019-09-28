---
title: ST_INTERSECTS на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System ST_INTERSECTS в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 549c6b69e9112a491060478e859338c14e977612
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349382"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
 Возвращает логическое выражение, указывающее, пересекается ли объект GeoJSON (точка, многоугольник или LineString), указанный в первом аргументе, со вторым объектом GeoJSON (точка, многоугольник или LineString).  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*spatial_expr*  
   — Это выражение геообъектной точки, многоугольника или объекта LineString.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает логическое значение.  
  
## <a name="examples"></a>Примеры
  
  В примере ниже показано, как найти все области, пересекающиеся с заданным многоугольником.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Результирующий набор:  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="next-steps"></a>Следующие шаги

- [Пространственные функции Azure Cosmos DB](sql-query-spatial-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
