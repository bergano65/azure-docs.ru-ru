---
title: ATAN на языке запросов Azure Cosmos DB
description: Сведения о том, как функция ATAN Azure Cosmos DB в языке SQL в радианах Возвращает угол, тангенс которого является указанным числовым выражением.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302684"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Возвращает угол в радианах, тангенс которого равен указанному числовому выражению. Эта функция арктангенсом.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере возвращается `ATAN` значение указанного значения.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Результирующий набор:  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство с Azure Cosmos DB](introduction.md)
