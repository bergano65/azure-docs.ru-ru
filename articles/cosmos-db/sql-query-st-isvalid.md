---
title: ST_ISVALID языка запросов Azure Cosmos DB
description: Сведения о ST_ISVALID системных функций SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c1af008889ece71bc2158e0a74c30ef17d24207f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080062"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает логическое значение, указывающее, является ли действительным выражение GeoJSON (точка, многоугольник или LineString).  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*spatial_expr*  
   Является геообъектным выражением точки, многоугольника или LineString.  
  
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
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
