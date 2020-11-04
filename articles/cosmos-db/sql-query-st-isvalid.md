---
title: ST_ISVALID языка запросов Azure Cosmos DB
description: Сведения о ST_ISVALID системных функций SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9b20e57672e86c2b5a6a2a25151d779ea7bc92f3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335167"
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

## <a name="next-steps"></a>Дальнейшие шаги

- [Пространственные функции Azure Cosmos DB](sql-query-spatial-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
