---
title: ST_ISVALIDDETAILED на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функции системы S'L ST_ISVALIDDETAILED в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349355"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
 Возвращает значение JSON, содержащее логическое значение, указывающее, является ли выражение GeoJSON (точка, многоугольник или LineString) действительным. Если оно является недействительным, то возвращаемое значение также содержит строку с описанием причины.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*spatial_expr*  
   Является точкой GeoJSON или выражением полигона.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает значение JSON, содержащее логическое значение, указывающее, является ли выражение точки или многоугольника GeoJSON действительным. Если оно является недействительным, возвращаемое значение также содержит строку с описанием причины.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример, как проверить достоверность (с подробной информацией) с помощью `ST_ISVALIDDETAILED`.  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Результирующий набор:  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Пространственные функции Azure Cosmos DB](sql-query-spatial-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
