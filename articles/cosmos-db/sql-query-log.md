---
title: Вход на языке Azure Cosmos DB запросов
description: Сведения о системной функции LOG SQL в Azure Cosmos DB для получения натурального логарифма указанного числового выражения
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 74d07076fb322214348d52fe65dbc98bdff3cd44
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798267"
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
  
*base*  
   Дополнительный числовой аргумент, который задает основание логарифма.  
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает числовое выражение.  
  
## <a name="remarks"></a>Remarks
  
  По умолчанию функция LOG() возвращает натуральный логарифм. Основание логарифма можно изменить с помощью дополнительного параметра основания.  
  
  Натуральный логарифм — это логарифм по основанию **e**, где **e** — это иррациональная константа, равная приблизительно 2,718281828.  
  
  Натуральный логарифм экспоненты числа — это само число. Например, LOG( EXP( n ) ) = n. И экспонента натурального логарифма числа также является самым числом. Например, EXP( LOG( n ) ) = n.

  Эта системная функция не будет использовать индекс.
  
## <a name="examples"></a>Примеры
  
  В примере ниже объявляется переменная и возвращается значение логарифма указанной переменной (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Результирующий набор:  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 В приведенном ниже примере вычисляется `LOG` для экспоненты числа.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Результирующий набор:  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
