---
title: Язык запросов POWER in Azure Cosmos DB
description: Сведения о возAzure Cosmos DBии функциональной возможности SQL System.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "71349633"
---
# <a name="power-azure-cosmos-db"></a>МОЩНОСТЬ (Azure Cosmos DB)
 возвращает значение указанного выражения, возведенное в заданную степень.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr1*  
   Числовое выражение.  
  
*numeric_expr2*  
   Степень, в которую следует возвести *numeric_expr1*.  
  
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

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
