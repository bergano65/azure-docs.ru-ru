---
title: ГРАДУСы на языке запросов Azure Cosmos DB
description: Сведения о функции SQL System в ГРАДУСАХ в Azure Cosmos DB, чтобы получить соответствующий угол в градусах для угла, указанного в радианах
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5f432f5ef57c8eccc0cf629f00d2231364785b9a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871335"
---
# <a name="degrees-azure-cosmos-db"></a>ГРАДУСы (Azure Cosmos DB)
 Возвращает соответствующее значение угла в градусах для угла, указанного в радианах.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример возвращает число градусов в угле, равном PI/2 радиан.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Результирующий набор:  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
