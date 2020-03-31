---
title: ST_ISVALID на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функции системы S'L ST_ISVALID в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349365"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 Возвращает логическое значение, указывающее, является ли действительным выражение GeoJSON (точка, многоугольник или LineString).  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*spatial_expr*  
   Является выражением GeoJSON Point, Polygon или LineString.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  В примере ниже показано, как проверить допустимость точки с помощью функции ST_VALID.  
  
  Например, эта точка может иметь значение широты, которое не входит в допустимый диапазон значений (от –90 до 90), из-за чего запрос возвращает значение false.  
  
  По спецификации GeoJSON для многоугольника последняя пара координат должна совпадать с первой, чтобы фигура стала замкнутой. Точки внутри многоугольника должны указываться в порядке против часовой стрелки. Если точки указаны в порядке по часовой стрелке, то многоугольник представляет регион, расположенный снаружи от него.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Результирующий набор:  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Пространственные функции Azure Cosmos DB](sql-query-spatial-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
