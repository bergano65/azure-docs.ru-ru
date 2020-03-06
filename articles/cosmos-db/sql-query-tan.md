---
title: TAN на языке Azure Cosmos DB запросов
description: Сведения о функции SQL System TAN в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301987"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Возвращает тангенс заданного угла в радианах для указанного выражения.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Пример ниже вычисляет тангенс от PI()/2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Результирующий набор:  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
