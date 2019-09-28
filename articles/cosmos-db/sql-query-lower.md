---
title: НИЖЕ Azure Cosmos DB язык запросов
description: Сведения о системной функции SQL ниже в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3a9c122ef65772458b832d3b1651e7e63e42985e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349704"
---
# <a name="lower-azure-cosmos-db"></a>НИЖЕ (Azure Cosmos DB)
 Возвращает строковое выражение после преобразования символов верхнего регистра в нижний.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Является строковым выражением.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает строковое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере показано, как использовать `LOWER` в запросе.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Результирующий набор:  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>Следующие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
