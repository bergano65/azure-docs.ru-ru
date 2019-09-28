---
title: Вход на языке Azure Cosmos DB запросов
description: Дополнительные сведения о журнале системных функций SQL в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 16f2321eb48afacbc9636b5c0588b1ea3a01a284
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349738"
---
# <a name="log-azure-cosmos-db"></a>Журнал (Azure Cosmos DB)
 Возвращает натуральный логарифм от указанного числового выражения.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Аргументы
  
*numeric_expr*  
   Числовое выражение.  
  
*из*  
   Дополнительный числовой аргумент, который задает основание логарифма.  
  
## <a name="return-types"></a>Возвращаемые типы
  
  Возвращает числовое выражение.  
  
## <a name="remarks"></a>Примечания
  
  По умолчанию функция LOG() возвращает натуральный логарифм. Основание логарифма можно изменить с помощью дополнительного параметра основания.  
  
  Натуральный логарифм — это логарифм с основанием **e**, где **e** — это иррациональная константа приблизительно равная 2,718281828.  
  
  Натуральный логарифм экспоненты числа — это само число. Например, LOG( EXP( n ) ) = n. Экспонента натурального логарифма числа — это само число. Например, EXP( LOG( n ) ) = n.  
  
## <a name="examples"></a>Примеры
  
  В примере ниже объявляется переменная и возвращается значение логарифма указанной переменной (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Результирующий набор:  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 В следующем примере вычисляется `LOG` для экспоненты числа.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Результирующий набор:  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Следующие шаги

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
