---
title: Использование ENDSWITH на языке запросов Azure Cosmos DB
description: Узнайте, как системная функция SQL ENDSWITH в Azure Cosmos DB возвращает значение логического типа, указывающее, заканчивается ли первое строковое выражение вторым
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dd34a41fd9757d91845eb53626b00381c7ddda7a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100292"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Возвращает значение логического типа, указывающее, заканчивается ли первое строковое выражение вторым.  
  
## <a name="syntax"></a>Синтаксис
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Аргументы
  
*str_expr1*  
   Строковое выражение.  
  
*str_expr2*  
   Строковое выражение, сравниваемое с концом *str_expr1* .

*bool_expr* Необязательное значение для игнорирования регистра. Если задано значение true, ENDSWITH будет выполнять поиск без учета регистра. Если значение не указано, оно равно false.
  
## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает логическое выражение.  
  
## <a name="examples"></a>Примеры
  
Пример ниже проверяет, заканчивается ли строка abc символами b и bC.  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 Результирующий набор:  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>Remarks

Эта системная функция будет использовать возможности [индекса диапазона](index-policy.md#includeexclude-strategy).

При увеличении количества элементов свойства в системной функции ENDSWITH будет увеличиваться потребление единиц запросов. Иными словами, если вы проверяете, заканчивается ли значение свойства определенной строкой, стоимость запроса в единицах запросов будет зависеть от количества возможных значений этого свойства.

Например, рассмотрим два свойства: town и country. Кратность town — 5000, а кратность country — 200. Ниже приведены два примера запросов.

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

Первый запрос, скорее всего, будет использовать больше единиц запросов, чем второй, так как кратность town выше, чем кратностью country.

Если размер свойства в EndsWith превышает 1 КБ для некоторых документов, обработчику запросов потребуется загрузить эти документы. В этом случае обработчик запросов не сможет полностью оценить EndsWith с помощью индекса. При наличии большого количества документов с размерами свойств, превышающими 1 КБ, плата за ЕДИНИЦу для EndsWith будет высока.

## <a name="next-steps"></a>Дальнейшие действия

- [Строковые функции Azure Cosmos DB](sql-query-string-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
