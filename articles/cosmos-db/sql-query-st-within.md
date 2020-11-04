---
title: ST_WITHIN языка запросов Azure Cosmos DB
description: Сведения о ST_WITHIN системных функций SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 565b3429f8e8398a93f981969f96984bdac52db0
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334996"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает логическое выражение, указывающее, располагается ли объект GeoJSON (точка, многоугольник или LineString), указанный в первом аргументе, внутри второго объекта GeoJSON (точка, многоугольник или LineString).  
  
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

## <a name="next-steps"></a>Дальнейшие шаги

- [Пространственные функции Azure Cosmos DB](sql-query-spatial-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
