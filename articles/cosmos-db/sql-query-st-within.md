---
title: ST_WITHIN на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функции системы S'L ST_WITHIN в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 07a339d82f5e4bea1ea0412a5d5b19522611b54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296122"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 Возвращает логическое выражение, указывающее, располагается ли объект GeoJSON (точка, многоугольник или LineString), указанный в первом аргументе, внутри второго объекта GeoJSON (точка, многоугольник или LineString).  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*spatial_expr*  
   Является выражением объекта GeoJSON, Polygon или LineString.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое значение.  
  
## <a name="examples"></a>Примеры
  
  Ниже приводится следующий пример, как найти все `ST_WITHIN`семейные документы в рамках полигона с помощью.  
  
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

Эта функция системы будет пользоваться [геопространственным индексом.](index-policy.md#spatial-indexes)

## <a name="next-steps"></a>Дальнейшие действия

- [Пространственные функции Azure Cosmos DB](sql-query-spatial-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
