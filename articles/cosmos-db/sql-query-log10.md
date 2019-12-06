---
title: LOG10 на языке запросов Azure Cosmos DB
description: Сведения о системной функции LOG10 SQL в Azure Cosmos DB для возврата десятичного логарифма указанного числового выражения
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5950a2a40a78716a76e49fcdaeb3129948b6a0e1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871063"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 Возвращает десятичный логарифм от указанного числового выражения.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expression*  
   Числовое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="remarks"></a>Примечания
  
  Функции LOG10 и POWER обратно связаны друг с другом. Например, 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Примеры
  
  В примере ниже объявляется переменная и возвращается значение LOG10 указанной переменной (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Результирующий набор:  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
