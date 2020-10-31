---
title: ST_ISVALIDDETAILED языка запросов Azure Cosmos DB
description: Сведения о ST_ISVALIDDETAILED системных функций SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: df2d4265393085a58699b3576ce2461ed63de317
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080045"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает значение JSON, содержащее логическое значение, указывающее, является ли выражение GeoJSON (точка, многоугольник или LineString) действительным. Если оно является недействительным, то возвращаемое значение также содержит строку с описанием причины.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*spatial_expr*  
   Является геообъектным выражением точки или многоугольника.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает значение JSON, содержащее логическое значение, указывающее, является ли выражение точки или многоугольника GeoJSON действительным. Если оно является недействительным, возвращаемое значение также содержит строку с описанием причины.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как проверить допустимость (с подробностями) с помощью `ST_ISVALIDDETAILED` .  
  
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
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
