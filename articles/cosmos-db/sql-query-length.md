---
title: Длина языка запросов Azure Cosmos DB
description: Сведения о ДЛИНе системной функции SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303721"
---
# <a name="length-azure-cosmos-db"></a>Длина (Azure Cosmos DB)
 Возвращает число символов указанного строкового выражения.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Оцениваемое строковое выражение.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="examples"></a>Примеры
  
  Пример ниже возвращает длину строки.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Результирующий набор:  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Remarks

Эта системная функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
