---
title: ATN2 на языке запросов Azure Cosmos DB
description: Узнайте о том, как системная функция ATN2 SQL в Azure Cosmos DB возвращает основное значение тангенса дуги y/x, выраженное в радианах.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 11321ef9d7b81af279b04e0e435b19c645cf3bcf
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871658"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Возвращает основное значение арктангенса y/x, выраженное в радианах.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  В примере ниже вычисляется ATN2 для указанных компонентов x и y.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Результирующий набор:  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
