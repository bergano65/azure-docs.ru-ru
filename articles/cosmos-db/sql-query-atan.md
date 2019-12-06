---
title: ATAN на языке запросов Azure Cosmos DB
description: Сведения о том, как функция ATAN Azure Cosmos DB в языке SQL в радианах Возвращает угол, тангенс которого является указанным числовым выражением.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fd398e343a3e617ceb9f49ca8f31ee776a7f073
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873460"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Возвращает угол в радианах, тангенс которого равен указанному числовому выражению. Также называется арктангенсом.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере возвращается `ATAN` указанного значения.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Результирующий набор:  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
