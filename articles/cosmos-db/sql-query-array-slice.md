---
title: ARRAY_SLICE на языке запросов Запроса Azure Cosmos DB
description: Узнайте о том, как функция системы СЗЛ array в Azure Cosmos DB возвращает часть выражения массива
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a98cb17d22f41776ff788d12ced6aa988ad0b10e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303330"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 Возвращает часть выражения массива.
  
## <a name="syntax"></a>Синтаксис
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Аргументы
  
*arr_expr*  
   Является ли любое выражение массива.  
  
*num_expr*  
   Отсчитываемый от нуля числовой индекс, с которого начинается массив. Отрицательные значения могут использоваться для указания начального значения индекса относительно последнего элемента массива. Например, значение –1 представляет собой последний элемент в массиве.  

*num_expr* Дополнительное числовое выражение, которое устанавливает максимальное количество элементов в результирующем массиве.    

## <a name="return-types"></a>Типы возвращаемых данных
  
  Возвращает выражение массива.  
  
## <a name="examples"></a>Примеры
  
  Ниже приводится следующий пример, как получить `ARRAY_SLICE`различные фрагменты массива с помощью.  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Результирующий набор:  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="remarks"></a>Remarks

Эта система функция не будет использовать индекс.

## <a name="next-steps"></a>Дальнейшие действия

- [Array функции Azure Космос DB](sql-query-array-functions.md)
- [Система работает Azure Космос DB](sql-query-system-functions.md)
- [Введение в Azure Космос DB](introduction.md)
