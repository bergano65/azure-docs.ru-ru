---
title: COS на языке запросов Azure Cosmos DB
description: Сведения о системной функции SQL COS в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e64127b7af749af87b90331c3f175957cfa7f09
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351071"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Возвращает тригонометрический косинус указанного угла в радианах в указанном выражении.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере вычисляется `COS` указанного угла.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Результирующий набор:  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="next-steps"></a>Следующие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
