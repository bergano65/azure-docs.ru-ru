---
title: ОБРАТная Azure Cosmos DB языка запросов
description: Дополнительные сведения о функции SQL System в ОБРАТную Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b25b720b0919038ca024f7c8b11712d78fd44d52
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082850"
---
# <a name="reverse-azure-cosmos-db"></a>REVERSE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Возвращает строковое значение, где символы переставлены в обратном порядке справа налево.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как использовать `REVERSE` в запросе.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Результирующий набор:  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
