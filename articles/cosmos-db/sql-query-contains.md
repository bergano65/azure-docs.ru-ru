---
title: СОДЕРЖИТ в Azure Cosmos DB языке запросов
description: Сведения о том, как функция CONTAINS SQL System в Azure Cosmos DB возвращает логическое значение, указывающее, содержит ли первое строковое выражение вторую
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 59c0e2628bfde24200bd386adc858ba778790231
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871539"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
 Возвращает значение логического типа, указывающее, содержит ли первое строковое выражение второе.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr1*  
   Строковое выражение, в котором выполняется поиск.  
  
*str_expr2*  
   Искомое строковое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере проверяется, содержит ли "ABC" "AB", а если "ABC" содержит "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Результирующий набор:  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
