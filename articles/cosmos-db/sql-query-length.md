---
title: Длина языка запросов Azure Cosmos DB
description: Сведения о ДЛИНе системной функции SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aa430152415b1662a73a388a03ba6d4721c730f0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349754"
---
# <a name="length-azure-cosmos-db"></a>Длина (Azure Cosmos DB)
 Возвращает число символов указанного строкового выражения.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr*  
   Строковое выражение для вычисления.  
  
## <a name="return-types"></a>Возвращаемые типы
  
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

## <a name="next-steps"></a>Следующие шаги

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
