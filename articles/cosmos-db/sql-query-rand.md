---
title: RAND на языке запросов Azure Cosmos DB
description: Узнайте о функции системы S'L RAND в Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302225"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Космос DB)
 Возвращает случайно сгенерированное числовое значение от 0,1.
 
## <a name="syntax"></a>Синтаксис
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Типы возвращаемых данных

  Возвращает числовое выражение.

## <a name="remarks"></a>Remarks

  `RAND` является недетерминированной функцией. Повторяющиеся вызовы `RAND` не возвращают одни и те же результаты.

## <a name="examples"></a>Примеры
  
  Следующий пример возвращает случайно сгенерированное числовое значение.
  
```sql
SELECT RAND() AS rand 
```  
  
 Результирующий набор:  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Математические функции Azure Космос DB](sql-query-mathematical-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
