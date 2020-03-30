---
title: ST_DISTANCE на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функциях системы S'L ST_DISTANCE в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537301"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Возвращает расстояние между двумя выражениями GeoJSON Point, Polygon, MultiPolygon или LineString. Чтобы узнать больше, смотрите статью [о геопространственных данных и данных о местоположении Geospatial и GeoJSON.](sql-query-geospatial-intro.md)
  
## <a name="syntax"></a>Синтаксис
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*spatial_expr*  
   Любое допустимое выражение объекта GeoJSON (точка, многоугольник или LineString).  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение, указывающее расстояние. При использовании эталонной системы по умолчанию это значение указывается в метрах.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как вернуть все семейные документы, `ST_DISTANCE` которые находятся в пределах 30 км от указанного местоположения, используя встроенную функцию. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Результирующий набор:  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Remarks

Эта функция системы будет пользоваться [геопространственным индексом.](index-policy.md#spatial-indexes)

## <a name="next-steps"></a>Дальнейшие действия

- [Пространственные функции Azure Cosmos DB](sql-query-spatial-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
