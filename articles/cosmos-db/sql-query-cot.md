---
title: COT на языке запросов Azure Cosmos DB
description: Сведения о том, как системная функция SQL котангенса (COT) в Azure Cosmos DB возвращает тригонометрический котангенс указанного угла в радианах в указанном числовом выражении
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78299493"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
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
  
  В следующем примере вычисляется `COT` указанный угол.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Результирующий набор:  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
