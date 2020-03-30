---
title: ST_INTERSECTS на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функции системы S'L ST_INTERSECTS в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f3c3878956b90ffb45556ed819046af9eb7618f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303143"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
 Возвращает логическое выражение, указывающее, пересекается ли объект GeoJSON (точка, многоугольник или LineString), указанный в первом аргументе, со вторым объектом GeoJSON (точка, многоугольник или LineString).  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*spatial_expr*  
   Является выражением объекта GeoJSON, Polygon или LineString.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
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

## <a name="remarks"></a>Remarks

Эта функция системы будет пользоваться [геопространственным индексом.](index-policy.md#spatial-indexes)

## <a name="next-steps"></a>Дальнейшие действия

- [Пространственные функции Azure Cosmos DB](sql-query-spatial-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
