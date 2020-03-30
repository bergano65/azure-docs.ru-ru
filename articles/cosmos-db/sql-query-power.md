---
title: POWER на языке запросов Запроса Azure Cosmos DB
description: Узнайте о функции системы СЗЛ POWER в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349633"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Космос DB)
 возвращает значение указанного выражения, возведенное в заданную степень.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr1*  
   Числовое выражение.  
  
*numeric_expr2*  
   Это власть, к которой поднять *numeric_expr1*.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Следующий пример демонстрирует возведение числа в степень 3 (в куб).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Результирующий набор:  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
