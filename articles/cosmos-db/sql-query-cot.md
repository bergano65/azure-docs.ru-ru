---
title: COT на языке запросов Azure Cosmos DB
description: Узнайте о том, как система Cotangent (COT) S'L в Azure Cosmos DB возвращает тригонометрический котанген указанного угла, в радианах, в указанном численном выражении
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299493"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Космос DB)
 Возвращает тригонометрический котангенс указанного угла в радианах в указанном числовом выражении.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример вычисляет `COT` указанный угол.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Результирующий набор:  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
