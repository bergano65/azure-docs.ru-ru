---
title: PI на языке запросов Azure Cosmos DB
description: Сведения о функции SQL System PI в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349650"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Возвращает значение константы "пи".  
  
## <a name="syntax"></a>Синтаксис
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В следующем примере возвращается значение `PI`.  
  
```sql
SELECT PI() AS pi 
```  
  
 Результирующий набор:  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Следующие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
