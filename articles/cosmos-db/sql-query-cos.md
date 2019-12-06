---
title: COS на языке запросов Azure Cosmos DB
description: Сведения о том, как косинус (COS) SQL System в Azure Cosmos DB возвращает тригонометрический косинус указанного угла в радианах в указанном выражении
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 05546ef531059e05a1426a288a48446d63d8e5df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873409"
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

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
